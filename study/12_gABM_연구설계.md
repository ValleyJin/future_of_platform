# 12장. LLM 기반 생성적 행위자 모형(gABM)으로 이 연구를 한다면

## 12.1 이 장의 전제와 범위

리뷰 논문 [[1](#ref-1)]은 "LLM 구동 에이전트로 행동 정책을 대체하라"고 제안하지만, 연구를 어떻게 설계하고 무엇을 증명해야 하는지는 말하지 않는다. 이 장은 그 빈자리를 채운다. 대부분의 내용은 교재가 구성한 것이며, 논문의 권고와 단서를 지키도록 짰다. 9장에서 쓴 표지를 이어서 쓴다. 【논문】은 논문이 실제로 말한 것, 【전략】은 교재가 붙인 설계다.

읽는 사람은 다음을 가정한다. LLM 에이전트로 소셜미디어나 추천 시스템을 시뮬레이션해 학위논문이나 저널 논문을 쓰려 하고, 6장(LLM 에이전트의 제안과 한계)과 9장(권고와 구현 전략)을 읽었다.

**gABM이란.** generative agent-based modeling, 곧 생성적 행위자 기반 모형이다. 기존 행위자 기반 모형(ABM)에서 각 행위자는 연구자가 손으로 적은 규칙("추천 목록의 첫 항목을 0.3 확률로 클릭한다")을 따른다. gABM은 그 규칙 자리에 대규모 언어모델을 놓는다. 에이전트는 자기 성향과 지금까지 본 것을 맥락으로 받아 다음 행동을 스스로 생성한다 [[57](#ref-57), [58](#ref-58), [59](#ref-59)]. 소셜미디어 맥락에서는 Törnberg 외 [[81](#ref-81)]가 대안 피드 알고리즘을 평가했고, Wang 외 [[61](#ref-61)]가 에코챔버의 출현을, Larooij와 Törnberg [[60](#ref-60)]가 친사회적 조치의 효과를 다뤘다.

## 12.2 왜 gABM인가, 그리고 왜 의심받는가

**【논문】 gABM이 메울 빈자리.** 논문 [[1](#ref-1)]이 시뮬레이션 계열 확장에 기대하는 것은 네 가지다. 사용자 성향에 따라 증폭이 누구에게 일어나는지 보는 것, 몇 달에서 몇 년이 걸리는 피드백 루프를 돌려 보는 것, 사람에게 윤리적으로 시킬 수 없는 조건을 시험하는 것, 랭킹 목표와 콘텐츠 조정(moderation) 정책과 인터페이스를 바꿔 가며 반사실 시나리오를 훑는 것이다. 여기에 접근권 문제도 있다. 플랫폼이 협력하지 않으면 알고리즘을 바꾸는 실험 자체가 불가능한데(8장), 시뮬레이션은 그 벽을 우회한다.

**【논문】 그러나 논문은 시뮬레이션에 회의적이다.** 피드백 루프를 다룬 절에서 이렇게 쓴다. 지금까지 몇몇 연구가 시뮬레이션으로 이 동역학을 탐구해 알고리즘이 그런 루프를 만들 "이론적 가능성"을 보였지만, "문제의 이론적 가능성이 아무리 그럴듯해도 그것이 실증적 중요성을 뜻하지는 않는다." 그래서 "피드백 루프의 실제 확산 정도와 인과 효과를 밝히려면 전혀 새로운 종류의 실증 연구와 연구 설계가 필요하다" [[1](#ref-1), [63](#ref-63), [64](#ref-64), [65](#ref-65)]. LLM 에이전트에 대해서도 "행동 충실도가 대부분 검증되지 않았다"고 못 박는다 [[82](#ref-82), [84](#ref-84)].

**【전략】 이 장의 출발점.** 그러므로 gABM 연구의 심사위원을 이 논문의 저자라고 생각하고 설계해야 한다. 질문은 하나다. **"당신의 시뮬레이션을 왜 믿어야 하는가?"** 이 장의 나머지는 그 질문에 답하는 순서로 짰다. 시뮬레이션에서 무엇이 나왔는지를 보이기 전에, 그 시뮬레이션이 믿을 만하다는 것을 먼저 증명해야 한다.

## 12.3 제안하는 논문 목차

**【전략】** 학위논문이나 긴 저널 논문을 기준으로 짰다. 짧은 논문이라면 3부와 4부를 본문으로 삼고 나머지를 부록으로 옮긴다.

| 부 | 장 | 내용 | 무엇을 증명하는가 |
|---|---|---|---|
| 1부. 문제 | 1 | 서론: 알고리즘 몫과 사용자 몫을 가르는 문제 | 문제가 풀리지 않았음 |
| | 2 | 기존 증거의 한계: 단기 실험, 접근권, 피드백 루프 | gABM이 아니면 답할 수 없는 질문이 있음 |
| | 3 | 연구 질문과 가설 (사전등록) | 질문이 설계와 데이터의 힘 안에 있음 |
| 2부. 모형 | 4 | 이론 틀: 공급과 수요, 피드백 루프 | 모형이 논문의 Fig. 2 구조와 대응함 |
| | 5 | 모형 설계: 에이전트, 추천 알고리즘, 콘텐츠 생태계 | 구성 요소가 실제 플랫폼의 어느 부분에 대응하는지 |
| | 6 | 보정(calibration): 실제 분포에 맞추기 | 초기 상태가 실제 세계를 닮았음 |
| 3부. 검증 | 7 | 행동 충실도 벤치마크 | **증명 1**: 에이전트가 사람처럼 행동함 |
| | 8 | 보정에 쓰지 않은 사실의 재현 | **증명 2**: 우연한 맞춤이 아님 |
| | 9 | 조작 점검(manipulation check) | **증명 3**: 반사실 조작이 의도대로 작동함 |
| 4부. 결과 | 10 | 실험 1: 알고리즘을 바꾼다 (상대적 증폭) | **증명 4a**: 알고리즘의 몫 |
| | 11 | 실험 2: 행동을 바꾼다 (반사실 에이전트) | **증명 4b**: 사용자 선택의 몫 |
| | 12 | 실험 3: 장기 피드백과 선호의 내생성 | **증명 5**: gABM만의 기여 |
| 5부. 한계 | 13 | 민감도와 견고성 | **증명 6**: 결과가 설정에 좌우되지 않음 |
| | 14 | 해석의 범위와 하지 않는 주장 | 과잉 일반화를 스스로 차단함 |
| | 15 | 결론과 후속 연구 | |

**【전략】 순서가 중요한 이유.** 3부(검증)가 4부(결과)보다 앞에 온다. 검증을 부록으로 미루면 심사자는 결과를 읽기 전에 믿음을 정하지 못한다. 논문 [[1](#ref-1)]의 권고 3 셋째 항목이 "신중한 방법론적 검증을 제공하라"인 것도 같은 뜻이다.

## 12.4 증명 1: 행동 충실도

행동 충실도(behavioral fidelity)는 가상 사용자가 실제 사람과 얼마나 같이 행동하는가를 뜻한다. 클릭 패턴, 한 콘텐츠에 머무는 시간, 추천 목록에서 무엇을 고르는가처럼 미시적인 수준의 닮음을 본다. 연구 결과가 바깥 세계에도 들어맞는가를 묻는 외적 타당도와는 다르다. 충실도는 에이전트 자체의 속성이고, 외적 타당도는 연구 결과의 속성이다. 두 개념의 비교표는 6장 6.5절에 있다.

**【논문】** 합성 에이전트를 데이터 기부 프로젝트, 브라우저 패널, 그 밖의 실제 온라인 행동 기록과 맞대어 평가하라. 이 벤치마크는 노출과 참여의 평균 패턴만이 아니라, 문제적 콘텐츠 소비의 대부분을 차지하는 소수 하위집단의 행동까지 재현하는지 봐야 한다 [[1](#ref-1)].

**【전략】 측정 지표.** 사람과 에이전트에게 같은 추천 목록을 주고 분포를 견준다. 분포 사이의 거리는 젠슨-섀넌 거리 $\mathrm{JS}(P \,\|\, Q)$처럼 0과 1 사이로 떨어지는 대칭 지표를 쓴다.

| 지표 | 무엇을 보는가 | 통과 기준(예시) |
|---|---|---|
| 추천 조건부 선택 분포 | 같은 목록에서 무엇을 고르는가 | $\mathrm{JS} \le 0.10$ |
| 체류 시간 분포 | 얼마나 오래 보는가 | 중앙값 오차 $\le 15\%$ |
| 세션 길이 | 한 번에 몇 개를 소비하는가 | 분포 거리 $\mathrm{JS} \le 0.10$ |
| 소비 집중도 | 상위 1% 점유율, 지니 계수 | 상위 1% 점유율 오차 $\le 5$%p |
| 이념 분포와 교차 노출 | 얼마나 치우치고 얼마나 섞이는가 | 겹침 비율 오차 $\le 10$%p |
| 이탈률 | 시간이 지나며 떠나는 비율 | 추세 방향 일치 |

**【전략】 꼬리 집단을 따로 본다.** 위 지표를 전체 표본과 상위 1% 소비자 집단에서 각각 계산한다. 논문 [[1](#ref-1)]이 강조하듯 문제는 꼬리에 있으므로, 전체 평균만 맞고 꼬리가 틀리면 그 모형으로는 정작 연구하려는 현상을 다룰 수 없다.

**【전략】 통과 기준은 실행 전에 정한다.** 숫자를 본 뒤에 기준을 정하면 검증이 아니다. 사전등록 문서에 기준과 실패 시 행동(모형 수정 후 재검증, 또는 범위 축소)을 함께 적는다.

**실패했을 때.** 결과를 발표하지 않는 것이 아니라, 충실도가 통과한 범위에서만 주장한다. 예를 들어 평균은 통과하고 꼬리가 실패했다면 "일반 사용자의 동역학"까지만 말하고 극단 소비는 다루지 않는다.

## 12.5 증명 2: 보정에 쓰지 않은 사실의 재현

**【전략】** 이것이 "시뮬레이션은 이론적 가능성만 보여준다"는 비판에 대한 가장 직접적인 답이다. 실제 세계의 알려진 사실을 두 묶음으로 나눈다. 한 묶음으로 모형을 맞추고(보정), 다른 묶음은 손대지 않고 남겨 두었다가 모형이 스스로 맞히는지 본다(검증). 기계학습의 훈련 집합과 시험 집합 분리와 같은 발상이다.

| 용도 | 맞춰야 할 사실 | 출처 |
|---|---|---|
| 보정 | 가짜뉴스가 전체 뉴스 소비의 1% 미만 | [[23](#ref-23)] |
| 보정 | 상위 1% 사용자가 가짜뉴스 소비의 80% 차지 | [[21](#ref-21)] |
| 보정 | 사용자 0.6%가 극단 채널 시청 시간의 80% 차지 | [[39](#ref-39)] |
| 검증 | 뉴스 노출의 75% 이상을 같은 성향에서 받는 사용자 20.6% | [[36](#ref-36)] |
| 검증 | 강한 당파적 분리를 보이는 온라인 뉴스 소비자 4% | [[32](#ref-32)] |
| 검증 | 추천만 따르면 극단 취향 사용자가 오히려 온건해짐 | [[45](#ref-45)] |
| 검증 | 알고리즘 추천을 켜면 태도가 움직이고, 꺼도 되돌아오지 않음 | [[54](#ref-54)] |
| 검증 | 역시간순 추천으로 3개월 바꿔도 태도 변화 없음 | [[33](#ref-33)] |
| 검증 | 우파 정당 콘텐츠가 좌파보다 더 증폭 | [[31](#ref-31)] |
| 검증 | 알고리즘이 다른 여러 플랫폼에서 저품질 뉴스가 더 높은 참여 | [[51](#ref-51)] |

**【전략】 무엇을 보고하는가.** 검증용 사실마다 모형이 낸 값과 실제 값, 그리고 사전에 정한 허용 오차를 표로 낸다. 특히 [[54](#ref-54)]의 비대칭(켜면 움직이고 꺼도 안 돌아옴)은 재현 난도가 높다. 이것을 재현하면 모형이 피드백 루프를 제대로 담았다는 강한 증거가 된다. 팔로우 목록이 남아서 생기는 현상이므로, 모형에 사회적 연결의 누적이 들어 있어야 나온다.

## 12.6 증명 3: 조작 점검

**【전략】** 반사실을 만들었다고 말하려면, 바꾸려 한 것만 바뀌고 나머지는 그대로라는 것을 보여야 한다. 실험심리학의 조작 점검(manipulation check)과 같다.

| 조작 | 확인할 것 |
|---|---|
| 알고리즘을 기준선으로 바꿈 | 에이전트의 성향 분포와 초기 연결망이 두 조건에서 같은가 |
| 에이전트에게 "추천만 따르라" 규칙 부여 | 추천 목록 자체가 두 조건에서 같은 방식으로 생성되는가 |
| 페르소나 성향을 조절 | 의도한 축만 움직이고 다른 축(활동량 등)은 그대로인가 |
| 무작위 배정 | 조건 간 사전 특성 균형이 맞는가 |

**【전략】 기준선을 명시한다.** 5장에서 강조했듯 "증폭됐다"는 말에는 "무엇에 비해서"가 붙어야 한다 [[43](#ref-43)]. gABM에서는 기준선을 자유롭게 만들 수 있다는 것이 장점이자 함정이다. 역시간순, 무작위 순서, 인기순, 과거 버전을 모두 돌려 보고 기준선에 따라 결론이 얼마나 달라지는지 보고한다. 기준선 하나만 쓰고 "증폭됐다"고 쓰면 안 된다.

## 12.7 증명 4: 알고리즘의 몫과 사용자의 몫

**【논문】** 권고 3의 핵심이다. 알고리즘을 바꾸고 행동을 고정하는 전략과, 알고리즘을 고정하고 행동을 바꾸는 전략을 결합해 알고리즘의 공급과 사용자의 수요를 갈라내라 [[1](#ref-1), [31](#ref-31), [45](#ref-45)].

**【전략】 형식화.** 추천 알고리즘을 $f$, 사용자 행동 규칙을 $g$라 하고, 관찰되는 소비 결과를 $C(f, g)$라 쓴다. 기준선을 $f_0$(예: 역시간순), 행위 주체성이 없는 규칙을 $g_0$(추천만 따름)라 하면 두 몫은 다음과 같다.

$$
\Delta_{\text{alg}} = C(f, g) - C(f_0, g), \qquad \Delta_{\text{user}} = C(f, g) - C(f, g_0)
$$

여기서 중요한 점이 하나 있다. 두 몫을 더해도 전체가 되지 않는다.

$$
C(f, g) - C(f_0, g_0) = \Delta_{\text{alg}} + \Delta_{\text{user}} + \Delta_{\text{inter}}
$$

$\Delta_{\text{inter}}$는 알고리즘과 사용자가 서로를 밀어 주며 생기는 몫이다. 관찰 연구나 한 번의 실험으로는 이 항을 떼어낼 수 없다. **gABM은 네 조건 $(f, g)$, $(f_0, g)$, $(f, g_0)$, $(f_0, g_0)$을 모두 돌릴 수 있으므로 이 항을 직접 추정한다.** 이것이 기존 방법과 갈리는 첫 지점이다.

**【전략】 검증 조건.** 이 분해가 믿을 만하려면 $\Delta_{\text{alg}}$가 Huszár 외 [[31](#ref-31)]의 증폭률과, $\Delta_{\text{user}}$가 Hosseinmardi 외 [[45](#ref-45)]의 봇 실험 결과와 같은 방향이고 비슷한 크기여야 한다. 어긋난다면 모형을 고치거나, 왜 어긋나는지를 설명해야 한다.

## 12.8 증명 5: 고유 기여, 선호의 내생성과 장기 피드백

**【논문】** 리뷰 [[1](#ref-1)]가 반복해서 말하는 한계가 있다. "사용자 선호와 알고리즘 노출은 장기 피드백 루프로 얽혀 있으므로, 오늘의 선호가 과거의 알고리즘 노출에 의해 얼마나 형성됐는지는 관찰 증거만으로는 알 수 없다." 관찰 데이터로는 원리상 답할 수 없다고 못 박은 질문이다.

**【전략】 이것이 gABM의 존재 이유다.** 시뮬레이션에서는 같은 에이전트에게 두 가지 과거를 살게 할 수 있다. 5장 5.9절의 표기로 쓰면, 사용자 $i$의 성향 $\theta_i$가 $T$ 주기 동안 알고리즘 $f$ 아래에서 변한 정도는 다음과 같다.

$$
\text{선호의 내생성} \;=\; \theta_i^{(T)}(f) - \theta_i^{(T)}(f_0)
$$

같은 사람이 알고리즘 $f$를 겪은 세계와 기준선 $f_0$를 겪은 세계를 나란히 돌려 성향을 견주는 것이다. 현실에서는 불가능하다. 이 양이 0에 가깝다면 사용자 선호는 알고리즘과 무관하게 형성된 것이고, 크다면 "수요"라고 부르던 것의 상당 부분이 실은 알고리즘이 만든 것이다. 논문의 핵심 주장(관찰된 수요를 외생적인 것으로 보지 말라)을 수치로 옮긴 것이 바로 이 양이다.

**【전략】 함께 볼 것.**

- **안정화 시점.** $\Delta_{\text{alg}}$와 $\Delta_{\text{user}}$가 주기에 따라 어떻게 변하고 언제 평평해지는가. 이것이 실제 실험 기간이 얼마나 되어야 하는지에 대한 답을 준다 [[1](#ref-1)].
- **단기 영 효과의 설명.** Meta 2020년 연구가 3개월 만에 효과를 못 본 것 [[33](#ref-33)]과 Gauthier 외가 7주 만에 본 것 [[54](#ref-54)]이 왜 다른지를 모형이 설명할 수 있어야 한다. 논문은 팔로우 목록에 영향이 흡수됐기 때문이라고 해석한다. 모형에서 사회적 연결을 껐다 켜 보면 이 해석을 시험할 수 있다.
- **시스템 수준 효과.** 개인 수준 실험이 못 잡는 창발과 간섭이 있다는 지적 [[53](#ref-53)]에 답할 수 있는 자리다. 개인 효과의 합과 전체 집단의 양극화 지표가 어떻게 갈리는지 보고한다.

**【전략】 주장의 강도를 조절한다.** 이 결과는 "실제 세계에서 선호가 이만큼 알고리즘 탓이다"가 아니라, "충실도 검증을 통과한 모형 안에서 이만큼이다"이다. 12.10절에서 다시 다룬다.

## 12.9 증명 6: 견고성과 민감도

**【논문】** 시드 콘텐츠, 계정의 로그인 상태, 실험 기간 같은 사소해 보이는 결정에도 결과가 뒤집힐 수 있으므로 문서화와 재현 기준이 필요하다 [[1](#ref-1), [69](#ref-69)].

**【전략】 gABM에서 바뀌는 것마다 결과를 보고한다.**

| 흔드는 축 | 왜 |
|---|---|
| 난수 시드 | 같은 설정에서 실행 간 분산 |
| 프롬프트 표현 | 문장을 바꾸면 행동이 달라지는가 |
| 온도 등 생성 설정 | 무작위성 수준의 영향 |
| 모델과 버전 | 다른 모델에서도 같은 방향이 나오는가 |
| 에이전트 수와 주기 수 | 규모에 따라 결론이 바뀌는가 |
| 기준선 선택 | 12.6절 |

**【전략】 순환성 검사.** LLM이 바로 그 플랫폼의 콘텐츠로 학습됐다면, 에이전트가 콘텐츠에 반응하는 것인지 자기 학습 데이터를 되뇌는 것인지 가릴 수 없다. 학습 컷오프 이후에 생산된 콘텐츠로 벤치마크를 따로 구성해 결과가 같은지 본다 [[82](#ref-82)].

**【전략】 페르소나 편향 검사.** LLM에 인구통계를 주고 사람을 흉내 내게 하면 집단마다 오차가 다르게 나타난다 [[82](#ref-82)]. 성별, 연령, 정치 성향 집단별로 충실도 지표를 따로 계산해 보고한다. 특정 집단만 잘 흉내 내는 모형으로 전체를 말하면 결론이 그 집단 쪽으로 치우친다.

## 12.10 하지 말아야 할 주장

**【전략】** 심사에서 가장 많이 깎이는 대목이다. 미리 선을 그어 두면 오히려 논문이 단단해진다.

| 하지 않을 주장 | 대신 할 주장 |
|---|---|
| "실제 세계에서 알고리즘이 X% 기여한다" | "충실도 검증을 통과한 모형 안에서 X%이며, 실제 인과 연구의 추정치와 같은 방향이다" |
| "이 시뮬레이션이 외적 타당도를 검증했다" | "행동 충실도를 검증했다. 외적 타당도는 다른 맥락에서의 반복이 필요하다" (6장 6.5절) |
| "에이전트가 인간을 대체한다" | "인간 실험을 보완하며, 사람에게 시킬 수 없는 조건을 다룬다" [[1](#ref-1)] |
| "장기 효과를 측정했다" | "모형 안의 장기 동역학을 추정했고, 단기 실증 결과와 어긋나지 않는다" |
| "에코챔버가 생기는 것을 보였다" | "이 조건에서 생길 수 있음을 보였다. 실제 확산 정도는 기술 통계의 몫이다" [[1](#ref-1)] |

마지막 줄은 논문이 시뮬레이션에 던진 바로 그 비판이다. "이론적 가능성이 실증적 중요성을 뜻하지는 않는다." 그래서 gABM 논문은 서론에서 실제 세계의 확산 정도 수치를 먼저 제시하고, 자기 연구가 그 규모 위에서 원인을 다룬다는 것을 밝히는 편이 좋다 [[23](#ref-23), [21](#ref-21)].

## 12.11 데이터와 자원

**【전략】**

| 필요한 것 | 왜 | 대안 |
|---|---|---|
| 실제 행동 기록 (노출 + 참여) | 충실도 벤치마크의 기준 [[1](#ref-1)] | 데이터 기부, 브라우저 패널, 상업 패널 [[76](#ref-76)] |
| 꼬리 집단이 포함된 표본 | 꼬리 재현 검증 | 선별 문항으로 과대 표집 후 재가중 |
| 콘텐츠 자원 | 에이전트가 소비할 실제 콘텐츠 | 공개 아카이브, 학습 컷오프 이후 수집분 |
| 추천 알고리즘 구현 | 조작 대상 | 공개 추천 알고리즘, 또는 직접 구현 |
| 계산 자원 | 에이전트 수 × 주기 수 × 호출 수 | 작은 모형으로 예비 실행 후 규모 산정 |

**【전략】 비용을 미리 계산한다.** 에이전트 1,000명이 100주기를 돌면 호출이 최소 10만 번이다. 민감도 검사까지 곱하면 수십 배가 된다. 예비 실행으로 주기당 호출 수와 단가를 재고, 규모를 설계 단계에서 확정한다.

## 12.12 단계별 진행

**【전략】 최소 실행 가능 연구부터 시작한다.**

| 단계 | 기간 | 산출 |
|---|---|---|
| 0 | 1–2개월 | 규칙 기반 ABM으로 파이프라인 검증. counterfactual bot 결과 [[45](#ref-45)] 재현 |
| 1 | 2–3개월 | 실제 행동 기록 확보와 정제. 보정용·검증용 사실 분리 |
| 2 | 2–3개월 | 에이전트 설계와 보정. 충실도 벤치마크 1차 (증명 1) |
| 3 | 1–2개월 | 검증용 사실 재현 (증명 2), 조작 점검 (증명 3) |
| 4 | 2–3개월 | 실험 1·2와 분해 (증명 4) |
| 5 | 2–3개월 | 장기 피드백과 선호의 내생성 (증명 5) |
| 6 | 1–2개월 | 민감도 (증명 6), 집필 |

단계 2에서 충실도가 통과하지 못하면 4단계로 넘어가지 않는다. 이 순서를 지키는 것이 "왜 믿어야 하는가"에 대한 답이 된다.

## 12.13 논문의 권고와의 대응

**【전략】** 자기 연구가 논문 [[1](#ref-1)]의 여섯 권고 가운데 무엇을 다루고 무엇을 다루지 않는지 밝히면, 심사자가 범위를 오해하지 않는다.

| 논문의 권고 | gABM 연구에서 | 어느 장 |
|---|---|---|
| 1. 규모를 먼저 | 직접 다루지 않음. 기존 기술 통계를 앵커로 인용 | 1장 서론 |
| 2. 설계를 질문에 맞춤 | 사전등록, 층위와 기간을 명시 | 3장 |
| 3. 서로 보완하는 인과 식별 | **핵심.** 두 전략 결합과 방법론적 검증 | 7–12장 |
| 4. 신중하고 투명한 해석 | 주장의 범위 제한, 재현 패키지 | 13–14장 |
| 5. 접근 제도화 | 직접 다루지 않음. 시뮬레이션이 접근 문제를 우회한다는 점만 논의 | 2장 |
| 6. 참여와 복지, 목적함수 | 선택 사항. 다목적 목적함수를 시나리오로 넣으면 다룰 수 있음 (9.13절) | 12장 |

## 12.14 예상 반론과 답

**【전략】** 심사자가 던질 질문을 미리 적어 두고 각각에 어느 장이 답하는지 연결한다.

| 반론 | 답할 자리 |
|---|---|
| "시뮬레이션은 이론적 가능성만 보여준다" [[1](#ref-1)] | 증명 2. 보정에 쓰지 않은 실제 사실을 재현 |
| "LLM이 그 플랫폼 데이터로 학습됐다" | 12.9절 순환성 검사 |
| "에이전트는 평균만 흉내 내고 꼬리는 못 한다" | 12.4절 꼬리 집단 별도 벤치마크 |
| "LLM 페르소나는 고정관념을 재생산한다" [[82](#ref-82)] | 12.9절 집단별 충실도 보고 |
| "효과 크기가 프롬프트에 좌우된다" | 12.9절 민감도, 사전등록 |
| "드문 문제에 과한 도구를 쓴다" | 12.10절, 서론의 규모 앵커 |
| "왜 현장실험이 아니라 시뮬레이션인가" | 12.2절. 접근권과 기간, 윤리 |

## 12.15 요점

- gABM 연구의 첫 과제는 결과가 아니라 신뢰다. 검증을 결과보다 앞에 놓는다.
- 증명해야 할 것은 여섯 가지다. 행동 충실도, 보정에 쓰지 않은 사실의 재현, 조작 점검, 알고리즘과 사용자 몫의 분해, 장기 피드백과 선호의 내생성, 견고성이다.
- gABM만의 기여는 두 가지다. 상호작용 항 $\Delta_{\text{inter}}$의 추정과, 관찰 데이터로는 원리상 잴 수 없다고 논문이 말한 선호의 내생성 $\theta^{(T)}(f) - \theta^{(T)}(f_0)$이다.
- 충실도는 외적 타당도가 아니다. 모형 안의 결과를 실제 세계의 값으로 말하지 않는다.
- 논문의 저자가 심사위원이라고 생각하고 쓴다. 그들이 시뮬레이션에 던진 비판이 곧 이 연구가 넘어야 할 문턱이다.

## 참고문헌

<a name="ref-1"></a>[1] Hosseinmardi, H., Dutta, U., Rothschild, D., & Watts, D. J. (2026). Algorithmic systems, human agency and the future of platform research. *Nature Computational Science, 6*, 923–938. https://doi.org/10.1038/s43588-026-01038-1

<a name="ref-21"></a>[21] Grinberg, N., Joseph, K., Friedland, L., Swire-Thompson, B., & Lazer, D. (2019). Fake news on Twitter during the 2016 U.S. presidential election. *Science, 363*(6425), 374–378. https://doi.org/10.1126/science.aau2706

<a name="ref-23"></a>[23] Allen, J., Howland, B., Mobius, M., Rothschild, D., & Watts, D. J. (2020). Evaluating the fake news problem at the scale of the information ecosystem. *Science Advances, 6*(14), eaay3539. https://doi.org/10.1126/sciadv.aay3539

<a name="ref-31"></a>[31] Huszár, F., Ktena, S. I., O'Brien, C., Belli, L., Schlaikjer, A., & Hardt, M. (2022). Algorithmic amplification of politics on Twitter. *Proceedings of the National Academy of Sciences, 119*(1), e2025334119. https://doi.org/10.1073/pnas.2025334119

<a name="ref-32"></a>[32] Muise, D., Hosseinmardi, H., Howland, B., Mobius, M., Rothschild, D., & Watts, D. J. (2022). Quantifying partisan news diets in Web and TV audiences. *Science Advances, 8*(28), eabn0083. https://doi.org/10.1126/sciadv.abn0083

<a name="ref-33"></a>[33] Guess, A. M., Malhotra, N., Pan, J., et al. (2023). How do social media feed algorithms affect attitudes and behavior in an election campaign? *Science, 381*(6656), 398–404. https://doi.org/10.1126/science.abp9364

<a name="ref-36"></a>[36] González-Bailón, S., Lazer, D., Barberá, P., et al. (2023). Asymmetric ideological segregation in exposure to political news on Facebook. *Science, 381*(6656), 392–398. https://doi.org/10.1126/science.ade7138

<a name="ref-39"></a>[39] Chen, A. Y., Nyhan, B., Reifler, J., Robertson, R. E., & Wilson, C. (2023). Subscriptions and external links help drive resentful users to alternative and extremist YouTube channels. *Science Advances, 9*(35), eadd8080. https://doi.org/10.1126/sciadv.add8080

<a name="ref-43"></a>[43] Stray, J., Thorburn, L., & Bengani, P. (2023). Making amplification measurable. *Tech Policy Press*. https://www.techpolicy.press/making-amplification-measurable/

<a name="ref-45"></a>[45] Hosseinmardi, H., Ghasemian, A., Rivera-Lanas, M., Horta Ribeiro, M., West, R., & Watts, D. J. (2024). Causally estimating the effect of YouTube's recommender system using counterfactual bots. *Proceedings of the National Academy of Sciences, 121*(8), e2313377121. https://doi.org/10.1073/pnas.2313377121

<a name="ref-51"></a>[51] Mosleh, M., Allen, J., & Rand, D. G. (2025). Divergent patterns of engagement with partisan and low-quality news across seven social media platforms. *Proceedings of the National Academy of Sciences, 122*(44), e2425739122. https://doi.org/10.1073/pnas.2425739122

<a name="ref-53"></a>[53] Bak-Coleman, J. B., et al. (2025). *Moving towards informative and actionable social media research*. arXiv. https://doi.org/10.48550/arXiv.2505.09254

<a name="ref-54"></a>[54] Gauthier, G., Hodler, R., Widmer, P., & Zhuravskaya, E. (2026). The political effects of X's feed algorithm. *Nature, 652*, 416–423. https://doi.org/10.1038/s41586-026-10098-2

<a name="ref-57"></a>[57] Park, J. S., O'Brien, J. C., Cai, C. J., Morris, M. R., Liang, P., & Bernstein, M. S. (2023). Generative agents: Interactive simulacra of human behavior. In *Proceedings of the 36th Annual ACM Symposium on User Interface Software and Technology* (Article 2, pp. 1–22). ACM. https://doi.org/10.1145/3586183.3606763

<a name="ref-58"></a>[58] Argyle, L. P., Busby, E. C., Fulda, N., Gubler, J. R., Rytting, C., & Wingate, D. (2023). Out of one, many: Using language models to simulate human samples. *Political Analysis, 31*(3), 337–351. https://doi.org/10.1017/pan.2023.2

<a name="ref-59"></a>[59] Horton, J. J. (2023). *Large language models as simulated economic agents: What can we learn from homo silicus?* (NBER Working Paper No. 31122). National Bureau of Economic Research. https://doi.org/10.3386/w31122

<a name="ref-60"></a>[60] Larooij, M., & Törnberg, P. (2025). *Can we fix social media? Testing prosocial interventions using generative social simulation*. arXiv. https://arxiv.org/abs/2508.03385

<a name="ref-61"></a>[61] Wang, C., Liu, Z., Yang, D., & Chen, X. (2025). Decoding echo chambers: LLM-powered simulations revealing polarization in social networks. In *Proceedings of the 31st International Conference on Computational Linguistics* (pp. 3913–3923). Association for Computational Linguistics.

<a name="ref-63"></a>[63] Mansoury, M., Abdollahpouri, H., Pechenizkiy, M., Mobasher, B., & Burke, R. (2020). Feedback loop and bias amplification in recommender systems. In *Proceedings of the 29th ACM International Conference on Information & Knowledge Management* (pp. 2145–2148). ACM. https://doi.org/10.1145/3340531.3412152

<a name="ref-64"></a>[64] Jiang, R., Chiappa, S., Lattimore, T., György, A., & Kohli, P. (2019). Degenerate feedback loops in recommender systems. In *Proceedings of the 2019 AAAI/ACM Conference on AI, Ethics, and Society* (pp. 383–390). ACM. https://doi.org/10.1145/3306618.3314288

<a name="ref-65"></a>[65] Ribeiro, M. H., Veselovsky, V., & West, R. (2023). The amplification paradox in recommender systems. *Proceedings of the International AAAI Conference on Web and Social Media, 17*, 1138–1142. https://doi.org/10.1609/icwsm.v17i1.22223

<a name="ref-69"></a>[69] Brown, M. A., Bisbee, J., Lai, A., Bonneau, R., Nagler, J., & Tucker, J. A. (2022). *Echo chambers, rabbit holes, and algorithmic bias: How YouTube recommends content to real users*. SSRN. https://doi.org/10.2139/ssrn.4114905

<a name="ref-76"></a>[76] Ohme, J., et al. (2024). Digital trace data collection for social media effects research: APIs, data donation, and (screen) tracking. *Communication Methods and Measures, 18*(2), 124–141. https://doi.org/10.1080/19312458.2023.2181319

<a name="ref-81"></a>[81] Törnberg, P., Valeeva, D., Uitermark, J., & Bail, C. (2023). *Simulating social media using large language models to evaluate alternative news feed algorithms*. arXiv. https://arxiv.org/abs/2310.05984

<a name="ref-82"></a>[82] Bisbee, J., Clinton, J. D., Dorff, C., Kenkel, B., & Larson, J. M. (2024). Synthetic replacements for human survey data? The perils of large language models. *Political Analysis, 32*(4), 401–416. https://doi.org/10.1017/pan.2024.5

<a name="ref-84"></a>[84] Wang, Z. Z., et al. (2026). *How well does agent development reflect real-world work?* arXiv. https://doi.org/10.48550/arXiv.2603.01203
