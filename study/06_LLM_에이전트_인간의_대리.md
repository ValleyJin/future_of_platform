# 6장. LLM 에이전트를 인간의 대리로: 논문의 제안과 한계

## 6.1 질문과 짧은 답

**질문**: 이 논문에서 저자는 LLM을 이용해 인간의 대리(proxy)로서 실험에 활용하는 방법도 제안하는가?

**답**: 그렇다. 명시적으로 제안하며, "proxy"라는 단어를 직접 쓴다. 다만 "인간 피험자를 완전히 대체할 수 없다"는 단서를 같은 단락에 붙이고, 대리가 얼마나 믿을 만한지를 검증할 벤치마크부터 만들자고 한다 [[1](#ref-1)]. 이 장은 그 제안이 논문의 어디에 어떻게 놓여 있는지, 무엇을 기대하고 무엇을 경계하는지, 그리고 초보 연구자가 이 방법을 쓰려 할 때 무엇을 먼저 확인해야 하는지를 정리한다.

## 6.2 논문 안에서의 위치

논문 [[1](#ref-1)]은 이 제안을 네 곳에서 언급한다.

| 위치 | 내용 |
|---|---|
| Box 1, 권고 3 | "관찰 분석, 통제 실험, 그리고 합성 사용자(synthetic-user) 또는 노출-참여 설계를 결합해 알고리즘 공급과 사용자 수요를 가르고 시간에 따른 피드백을 포착하라" |
| 권고 3 본문 | counterfactual bot에서 디지털 트윈으로, 다시 LLM 구동 에이전트로 이어지는 확장 제안. "현실적인 대리(realistic proxies)"라는 표현이 여기에 나온다 |
| 권고 5 본문 | 플랫폼이 "검증된 연구자가 통제된 조건에서 사람 행동을 흉내 내는 자동화 에이전트를 배치할 수 있게 허용"하면 사용자의 능동적 선택, 노출 메커니즘, 콘텐츠 역학을 연구할 새 길이 열린다 |
| 걸림돌 3 (도구 검증) | transformer 계열 방법과 LLM이 주제·당파성·감정 측정뿐 아니라 "가상 에이전트 개발이나 선거 결과 예측 같은 더 복잡한 응용"에 쓰이고 있으며 [[81](#ref-81), [83](#ref-83)], 이 새 세대의 방법에도 같은 검증 원칙이 적용된다 |

즉 이 제안은 논문의 곁가지가 아니라 인과 식별 권고의 한 축이며, 플랫폼 접근 권고와 도구 검증 논의에까지 연결돼 있다.

## 6.3 계보: 손인형(sock puppet)에서 LLM 에이전트까지

논문 [[1](#ref-1)]은 LLM 에이전트를 갑자기 꺼내는 것이 아니라, 5장에서 다룬 방법들의 연장선에 놓는다.

| 단계 | 무엇인가 | 행동을 누가 정하나 | 개인화 상태 | 한계 |
|---|---|---|---|---|
| 손인형(sock puppet) [[12](#ref-12), [40](#ref-40)] | 연구자가 만든 가짜 계정 | 고정된 스크립트 | 없음 | 실제 사용자의 구독·이력을 반영하지 못함 |
| Counterfactual bot [[45](#ref-45)] | 실제 사용자의 이력을 재생한 봇 | 단순 규칙 ("추천만 클릭") | 있음 | 규칙이 단순해 사람의 다양성을 담지 못함. 공급만 측정 |
| 디지털 트윈 [[62](#ref-62)] | 실제 사용자의 행동 패턴으로 만든 복제본 | 의사결정 규칙("뇌") + 자동화 계정("팔") | 있음 | 규칙의 표현력에 한계 |
| LLM 에이전트 [[57](#ref-57), [58](#ref-58), [59](#ref-59)] | LLM이 행동을 생성하는 가상 사용자 | 맥락에 맞게 그때그때 선택 | 설정 가능 | 사람과 얼마나 비슷한지 미검증 |

논문의 논리는 이렇다. 이전의 봇 설계는 "고정된 시청 정책"을 부여했지만, LLM 기반 에이전트는 추천에 반응해 "콘텐츠 선택을 동적으로 조정"할 수 있다. LLM은 "맥락에 맞고 다양한 반응을 생성할 수 있어 실제 사용자 행동의 다양성에 더 가깝다." 정치적 사전 성향, 자극적 콘텐츠에 대한 취약성, 반대 의견에 대한 호기심이 서로 다른 사용자를 흉내 내면서도, 추천 알고리즘은 고정할 수 있다 [[1](#ref-1)]. 다시 말해 5장의 전략 2(알고리즘 고정, 사용자 행동 변화)를 훨씬 풍부한 "사용자 행동"으로 실행하는 것이다.

## 6.4 논문이 기대하는 네 가지 쓰임

논문 [[1](#ref-1)]이 LLM 에이전트로 할 수 있다고 쓰는 것은 다음 네 가지다.

1. **"누구에게, 어떤 행동 조건에서" 증폭이 일어나는지.** 알고리즘이 문제적 콘텐츠를 증폭하는지만이 아니라, 어떤 성향과 습관을 가진 사용자에게 그런 일이 생기는지를 체계적으로 바꿔 가며 볼 수 있다.
2. **장기간의 되풀이.** 에이전트는 추천 시스템과 "긴 시뮬레이션 기간에 걸쳐 반복해서" 상호작용할 수 있으므로, "단기 실험실 실험과 지속되는 실제 행동 사이의 간극을 메울" 수 있다. 피드백 루프처럼 여러 주기가 지나야 나타나는 효과를 볼 수 있다는 뜻이다.
3. **사람에게 시킬 수 없는 실험.** "인간 참가자로는 윤리적으로 검증할 수 없는 민감한 주제"를 연구할 수 있다.
4. **반사실 시나리오의 체계적 탐색.** 랭킹 목표, 조정 전략, 인터페이스 변경이 에코챔버의 출현이나 저품질 정보의 확산에 어떤 영향을 미치는지를 바꿔 가며 시험할 수 있다 [[60](#ref-60), [61](#ref-61), [81](#ref-81)].

논문은 이런 "합성이지만 사람 같은 환경"이 "참여 리듬, 체류 시간, 반응성의 현실적인 대리(realistic proxies)"를 만들어 낸다고 쓴다. 여기가 "proxy"라는 말이 등장하는 곳이다.

## 6.5 논문이 단 단서

같은 단락에서 논문 [[1](#ref-1)]은 다음을 분명히 한다.

- **완전한 대체는 불가능하다.** "LLM 기반 에이전트는 인간 피험자를 완전히 대체할 수 없다."
- **행동 충실도(behavioral fidelity)가 검증되지 않았다.** "현실적인 알고리즘 압력 아래서의 클릭 패턴, 체류 시간, 콘텐츠 선택 수준에서 그들의 행동 충실도는 대부분 검증되지 않았다" [[82](#ref-82), [84](#ref-84)].
- **보완적 위치다.** 이 접근은 관찰 연구와 현장실험을 "보완(complementing)"한다. 대체하는 것이 아니다.
- **검증이 먼저다.** 벤치마크를 만들고 검증하는 것 자체가 "중요한 향후 연구 방향"이다.

### 충실도와 외적 타당도는 어떻게 다른가

"충실도"는 논문의 "behavioral fidelity"를 옮긴 말이다. 외적 타당도와 관련은 있지만 같은 말은 아니다.

| | 행동 충실도 (behavioral fidelity) | 외적 타당도 (external validity) |
|---|---|---|
| 무엇의 속성인가 | 대리(에이전트) 자체의 속성 | 연구 결과의 속성 |
| 묻는 것 | 이 에이전트가 실제 사람과 같은 행동을 하는가 | 이 연구에서 얻은 결과가 연구 상황 밖에도 들어맞는가 |
| 수준 | 미시적. 클릭 패턴, 체류 시간, 추천에 대한 선택 | 거시적. 다른 집단, 다른 플랫폼, 다른 시점 |
| 검증 방법 | 실제 행동 기록과 맞대어 보는 벤치마크 | 재현 연구, 다른 맥락에서의 반복 |

둘의 관계는 이렇다. 행동 충실도는 에이전트 연구의 외적 타당도가 성립하기 위한 **전제 조건**이다. 에이전트가 사람처럼 행동한다는 것이 확인되지 않으면, 에이전트에게 일어난 일이 사람에게도 일어난다고 말할 근거가 없다. 그러나 충실도가 높다고 외적 타당도가 자동으로 따라오지는 않는다. 평균적인 사용자를 잘 흉내 내는 에이전트가 문제적 소비를 독점하는 꼬리 집단은 못 흉내 낼 수 있고, 2023년의 X 사용자를 잘 흉내 내는 에이전트가 2026년의 한국 사용자는 못 흉내 낼 수 있다. 논문이 벤치마크에서 "평균 패턴만이 아니라 소수 하위집단의 행동까지" 보라고 한 것은 바로 충실도 검증을 외적 타당도 쪽으로 한 걸음 넓히라는 요구다 [[1](#ref-1)].

3장 용어집에서 완전 시뮬레이션의 한계를 "외적 타당도 미검증"이라 쓴 것과 이 장에서 LLM 에이전트의 한계를 "행동 충실도 미검증"이라 쓴 것은 같은 문제를 다른 층위에서 본 것이다. 시뮬레이션 안의 사용자가 사람과 같은가(충실도)를 모르니, 시뮬레이션의 결과가 현실에 들어맞는가(외적 타당도)도 알 수 없다.

이 단서는 논문 전체의 태도와 같다. 논문은 어떤 도구든 "해당 영역에서 검증하지 않고 쓰는 것"을 걸림돌로 꼽으며(4장 4.9절), 시드 콘텐츠나 로그인 상태 같은 사소한 결정에도 결과가 뒤집힐 수 있음을 경고한다 [[69](#ref-69)]. LLM 에이전트라고 예외가 아니다.

## 6.6 벤치마크 제안

논문 [[1](#ref-1)]이 제안하는 검증 방법은 다음과 같다. 데이터 기부 프로젝트, 브라우저 패널, 그 밖의 실제 온라인 행동 기록과 합성 에이전트를 맞대어 본다. 이 벤치마크는 에이전트가 노출과 참여의 **평균 패턴**만이 아니라, 문제적 콘텐츠 소비의 대부분을 차지하는 **소수 하위집단의 행동**까지 재현하는지를 평가해야 한다. 여기까지가 논문의 내용이다.

**교재의 보충 (논문에 없음).** 이 제안을 실제 벤치마크로 만들려면 적어도 다음이 필요하다.

| 요소 | 내용 | 관련 문헌 |
|---|---|---|
| 기준 데이터 | 동의한 패널의 실제 노출·참여 기록. 화면에 보인 것과 클릭한 것을 모두 담아야 함 | [[39](#ref-39), [47](#ref-47), [76](#ref-76)] |
| 비교 조건 | 같은 추천 목록을 사람과 에이전트에게 제시했을 때의 선택 분포. 추천 조건부 클릭 확률, 체류 시간 분포, 세션 길이 | [[45](#ref-45)] |
| 집단별 재현 | 전체 평균이 아니라, 극단 콘텐츠 소비 상위 1% 같은 꼬리 집단의 행동을 따로 비교 | [[1](#ref-1)] |
| 시간에 따른 안정성 | 여러 주기가 지난 뒤의 노출 궤적이 사람과 같은 방향으로 움직이는지 | [[1](#ref-1)] |
| 민감도 보고 | 프롬프트, 모델 버전, 온도, 시드를 바꿨을 때 결과가 얼마나 달라지는지 | [[69](#ref-69)] |

이 가운데 어느 하나도 아직 표준이 없다. 그래서 논문이 "벤치마크 개발 자체가 연구 과제"라고 쓴 것이다.

## 6.7 교재의 보충: 알려진 위험

논문이 짧게 언급하고 지나간 위험을 조금 더 풀어 쓴다. 이 절은 교재의 보충이며, 인용된 문헌 외의 판단은 교재의 것이다.

- **합성 응답의 체계적 편차.** Bisbee 외 [[82](#ref-82)]는 LLM에 사람의 인구통계를 주고 설문에 답하게 했을 때, 그 답이 실제 사람의 답과 체계적으로 다르고 그 차이가 집단마다 달랐다고 보고한다. 대리가 특정 집단만 잘 흉내 낸다면, 그 대리로 얻은 결과는 그 집단 쪽으로 치우친다.
- **순환의 문제.** LLM은 바로 그 플랫폼의 콘텐츠로 학습됐을 가능성이 크다. 그렇다면 "가상 사용자"와 "콘텐츠" 사이의 독립성이 깨진다. 사용자가 콘텐츠에 반응하는 것인지, 콘텐츠로 만들어진 모델이 자기 학습 데이터에 반응하는 것인지를 가르기 어렵다.
- **접근 문제는 그대로다.** 에이전트를 실제 플랫폼에 붙이면 봇과 같은 약관·차단 위험을 안는다. 추천 알고리즘까지 시뮬레이션하면 접근 문제는 사라지지만 외적 타당도가 미검증이 된다(3장 용어집, 8장). 어느 쪽이든 공짜는 아니다.
- **개발 과정의 현실성.** Wang 외 [[84](#ref-84)]는 에이전트 개발이 실제 작업을 얼마나 반영하는지를 묻는다. 논문이 이 문헌을 행동 충실도 미검증의 근거로 인용한다.

## 6.8 인용된 선행 연구의 계보

논문 [[1](#ref-1)]이 이 제안의 근거로 인용한 문헌은 세 갈래다.

- **LLM을 사람의 대리로 쓰는 계열.** Park 외 [[57](#ref-57)]의 생성 에이전트(LLM 에이전트들이 가상 마을에서 상호작용), Argyle 외 [[58](#ref-58)]의 "실리콘 표본"(LLM으로 인간 설문 표본을 시뮬레이션), Horton [[59](#ref-59)]의 "homo silicus"(LLM을 경제 실험의 피험자로). 논문은 이들을 LLM 에이전트가 "실제 사용자 행동의 다양성에 더 가깝다"는 근거로 든다.
- **소셜미디어 시뮬레이션 계열.** Törnberg 외 [[81](#ref-81)]는 LLM으로 소셜미디어를 시뮬레이션해 대안 뉴스피드 알고리즘을 평가했고, Wang 외 [[61](#ref-61)]는 에코챔버의 출현을, Larooij와 Törnberg [[60](#ref-60)]는 친사회적 개입의 효과를 시뮬레이션했다. Chan 외 [[62](#ref-62)]는 LLM 기반 디지털 트윈을 제안했다. 논문은 이들을 "랭킹 목표, 조정 전략, 인터페이스 변경"을 시험하는 반사실 시나리오의 예로 든다.
- **비판 계열.** Bisbee 외 [[82](#ref-82)]와 Wang 외 [[84](#ref-84)]는 행동 충실도 미검증의 근거다.

세 갈래를 함께 인용한 것 자체가 논문의 입장을 보여 준다. 가능성은 크지만 검증은 안 됐다.

## 6.9 이 장의 요점

- 논문은 LLM 에이전트를 인간의 대리로 쓰는 방법을 명시적으로 제안하며, "realistic proxies"라는 표현을 쓴다.
- 이 제안은 손인형(sock puppet) → counterfactual bot → 디지털 트윈 → LLM 에이전트로 이어지는 계보의 끝에 놓이며, 5장의 전략 2(알고리즘 고정, 사용자 행동 변화)를 확장한 것이다.
- 기대하는 쓰임은 이질성 분석, 장기 되풀이, 윤리적으로 불가능한 실험, 반사실 시나리오 탐색이다.
- 단서는 분명하다. 완전 대체 불가, 행동 충실도 미검증, 보완적 위치, 검증이 먼저.
- 벤치마크는 평균만이 아니라 꼬리 집단의 재현까지 봐야 하며, 그 벤치마크를 만드는 것 자체가 연구 과제다.

## 참고문헌

<a name="ref-1"></a>[[1](#ref-1)] Hosseinmardi, H., Dutta, U., Rothschild, D., & Watts, D. J. (2026). Algorithmic systems, human agency and the future of platform research. *Nature Computational Science, 6*, 923–938. https://doi.org/10.1038/s43588-026-01038-1

<a name="ref-12"></a>[[12](#ref-12)] Sandvig, C., Hamilton, K., Karahalios, K., & Langbort, C. (2014). *Auditing algorithms: Research methods for detecting discrimination on internet platforms*. Paper presented at "Data and Discrimination: Converting Critical Concerns into Productive Inquiry," 64th Annual Meeting of the International Communication Association, Seattle, WA.

<a name="ref-39"></a>[[39](#ref-39)] Chen, A. Y., Nyhan, B., Reifler, J., Robertson, R. E., & Wilson, C. (2023). Subscriptions and external links help drive resentful users to alternative and extremist YouTube channels. *Science Advances, 9*(35), eadd8080. https://doi.org/10.1126/sciadv.add8080

<a name="ref-40"></a>[[40](#ref-40)] Haroon, M., Wojcieszak, M., Chhabra, A., Liu, X., Mohapatra, P., & Shafiq, Z. (2023). Auditing YouTube's recommendation system for ideologically congenial, extreme, and problematic recommendations. *Proceedings of the National Academy of Sciences, 120*(50), e2213020120. https://doi.org/10.1073/pnas.2213020120

<a name="ref-45"></a>[[45](#ref-45)] Hosseinmardi, H., Ghasemian, A., Rivera-Lanas, M., Horta Ribeiro, M., West, R., & Watts, D. J. (2024). Causally estimating the effect of YouTube's recommender system using counterfactual bots. *Proceedings of the National Academy of Sciences, 121*(8), e2313377121. https://doi.org/10.1073/pnas.2313377121

<a name="ref-47"></a>[[47](#ref-47)] Wang, S., Huang, S., Zhou, A., & Metaxa, D. (2024). Lower quantity, higher quality: Auditing news content and user perceptions on Twitter/X algorithmic versus chronological timelines. *Proceedings of the ACM on Human-Computer Interaction, 8*(CSCW), Article 57.

<a name="ref-57"></a>[[57](#ref-57)] Park, J. S., O'Brien, J. C., Cai, C. J., Morris, M. R., Liang, P., & Bernstein, M. S. (2023). Generative agents: Interactive simulacra of human behavior. In *Proceedings of the 36th Annual ACM Symposium on User Interface Software and Technology* (Article 2, pp. 1–22). ACM. https://doi.org/10.1145/3586183.3606763

<a name="ref-58"></a>[[58](#ref-58)] Argyle, L. P., Busby, E. C., Fulda, N., Gubler, J. R., Rytting, C., & Wingate, D. (2023). Out of one, many: Using language models to simulate human samples. *Political Analysis, 31*(3), 337–351. https://doi.org/10.1017/pan.2023.2

<a name="ref-59"></a>[[59](#ref-59)] Horton, J. J. (2023). *Large language models as simulated economic agents: What can we learn from homo silicus?* (NBER Working Paper No. 31122). National Bureau of Economic Research. https://doi.org/10.3386/w31122

<a name="ref-60"></a>[[60](#ref-60)] Larooij, M., & Törnberg, P. (2025). *Can we fix social media? Testing prosocial interventions using generative social simulation*. arXiv. https://arxiv.org/abs/2508.03385

<a name="ref-61"></a>[[61](#ref-61)] Wang, C., Liu, Z., Yang, D., & Chen, X. (2025). Decoding echo chambers: LLM-powered simulations revealing polarization in social networks. In *Proceedings of the 31st International Conference on Computational Linguistics* (pp. 3913–3923). Association for Computational Linguistics.

<a name="ref-62"></a>[[62](#ref-62)] Chan, A., et al. (2024). Redefining research crowdsourcing: Incorporating human feedback with LLM-powered digital twins. In *Extended Abstracts of the CHI Conference on Human Factors in Computing Systems*. ACM.

<a name="ref-69"></a>[[69](#ref-69)] Brown, M. A., Bisbee, J., Lai, A., Bonneau, R., Nagler, J., & Tucker, J. A. (2022). *Echo chambers, rabbit holes, and algorithmic bias: How YouTube recommends content to real users*. SSRN. https://doi.org/10.2139/ssrn.4114905

<a name="ref-76"></a>[[76](#ref-76)] Ohme, J., et al. (2024). Digital trace data collection for social media effects research: APIs, data donation, and (screen) tracking. *Communication Methods and Measures, 18*(2), 124–141. https://doi.org/10.1080/19312458.2023.2181319

<a name="ref-81"></a>[[81](#ref-81)] Törnberg, P., Valeeva, D., Uitermark, J., & Bail, C. (2023). *Simulating social media using large language models to evaluate alternative news feed algorithms*. arXiv. https://arxiv.org/abs/2310.05984

<a name="ref-82"></a>[[82](#ref-82)] Bisbee, J., Clinton, J. D., Dorff, C., Kenkel, B., & Larson, J. M. (2024). Synthetic replacements for human survey data? The perils of large language models. *Political Analysis, 32*(4), 401–416. https://doi.org/10.1017/pan.2024.5

<a name="ref-83"></a>[[83](#ref-83)] Li, L., et al. (2024). *Political-LLM: Large language models in political science*. arXiv. https://arxiv.org/abs/2412.06864

<a name="ref-84"></a>[[84](#ref-84)] Wang, Z. Z., et al. (2026). *How well does agent development reflect real-world work?* arXiv. https://doi.org/10.48550/arXiv.2603.01203
