---
layout: page
comment: true
title: "Understanding Agent Cooperation - KR"
date: 2020-09-27
category: Deepmind
tags: [Deepmind, Reinforcement Learning]
disqus: jongkyu-kim
---
2020-10-01

---
### 역자 주 <br>
> 원문은 2017년 딥마인드 블로그에 게시된 글로서, 같은 해 논문을 풀어쓴 내용입니다. (하단 링크 참조) <br><br>
> 죄수의 딜레마로 대표되는 기존 게임 이론은 주어진 환경에서 개인이 협동과 배척을 선택하는 과정을 설명하는데, 이는 하나의 행동만을 대상으로 하는 단순한 경우이므로 경우에 따른 보상을 표로 정리해서 최적의 행동을 쉽게 계산할 수 있었습니다. <br> <br>
> 그에 반해 본 글의 실험은 시간에 걸쳐서 계속 행동을 결정해야 하는 환경을 주고, 개인들이 본인에게 이익이 되는 협동과 배척을 어떻게 학습해나가는지를 deep multi-agent reinforcement learning 학습하여 시뮬레이션합니다. 이와 같은 환경을 글에서는 'Sequential Social Dilemmas' 로 명명하며, 좀더 현실에 가까운 사회 과학 분석 도구를 제공할 것으로 기대합니다. <br> <br>
> - 원문 링크: [Understanding Social Dilemmans](https://deepmind.com/blog/article/understanding-agent-cooperation) <br>
> - 논문 링크: [Multi-agent Reinforcement Learning in Sequential Social Dilemmas](https://arxiv.org/abs/1702.03037)
---

## Agent 협동의 이해

2017-02-09

__우리는 협력이 발생하는 과정을 모델링하기 위해서 multi-agent reinforcement learning 을 활용하였다. Sequential social dilemmas 라는 새로운 개념을 통해, 합리적인 agent 들이 어떻게 상호작용하고 자연 환경과 각자의 인지 능력에 따라 어떻게 협력적인 행동을 취하는지를 모델링할 수 있었다. 이 연구를 통해 우리는 경제, 교통, 환경 문제와 같은 복잡한 multi-agent 시스템에 대한 이해와 조정을 증진할 수 있는 가능성을 찾는다.__


자기 주도적인 사람들은 더 큰 업적을 위해 자주 협동한다. 각자가 자신의 안녕을 챙기고 다른 사람의 안녕은 무시하는 데만 관심이 있는 세상에서 어떻게 이런 일이 가능할까?

어떤 환경에서 이기적인 agent 들이 협동하는게 되는지에 대한 의문은 사회과학의 오랜 문제이다. 이 현상을 설명하는 가장 단순하면서도 세련된 모델은 게임 이론으로 잘 알려진 죄수의 딜레마이다.

두 명의 용의자가 체포되었고 독방에 갇혔다. 경찰은 자백 이외에는 용의자들을 유죄로 만들 증거가 없지만, 둘 모두를 각각 1년형에 처하게 할 수는 있다. 자백을 유도하기 위해서 용의자들에게 동시에 다음 조건을 내건다: 상대 용의자가 범인이라고 증언하면 ("배신") 풀려나게 해줄 것이지만, 상대 용의자는 3년형을 살게 된다. 만약에 두 용의자가 모두 증언하면 ("배신") 둘 모두 2년형을 살게 된다.

Agent 들이 합리적이라면 - 게임 이론적인 의미로 - 이 게임에서는 항상 배신해야 한다. 왜냐 하면 상대 용의자가 어떻게 행동하던지 배신하는 것이 이득이기 때문이다. 하지만, 모순적이게도, 두 용의자 모두 이렇게 행동하면 모두 감옥에서 2년을 살게 된다 - 만약에 둘이 합심해서 증언을 하지 않았다면 둘 다 1년형에 그쳤을 텐데 말이다. 이 모순을 우리는 social dilemma 라고 부른다.

최근 AI, 그 중에서도 deep reinforcement learning 학습 분야의 진보는 social dilemma 문제들을 다른 눈으로 바라볼 수 있는 도구를 제공한다. 전통적인 게임 이론 연구자들은 social dilemma 문제들을 협력과 대립 단 두가지 사이의 선택으로 모델링한다. 실제 세상에서는, 협력과 대립 모두 복잡한 행동을 요구하는데, 이는 일련의 복잡한 행동을 취하기 위한 agent 들의 학습을 포함한다. 이러한 상황을 여기서 sequential social dilemma 로 명하고, 이를 연구하기 위해서 심층 multi-agent reinforcmeent learning 을 통해 artificial agent 들을 학습시킨다.

예를 들어서, 다음에 언급할 Gathering 게임을 생각해보자: 빨강과 파랑, 두 agent 가 공존하는 공간을 배회하고 positive rewards 를 얻기 위해서 사과를 수집한다. 또한 상대 agent 를 광선으로 맞춰서 ("공격") 일시적으로 게임에서 퇴장시킬 수 있지만, 이 행동에 바로 보상이 주어지지는 않는다. 각 agent 들이  Gathering 게임을 플레이하는 모습을 아래 동영상에서 볼 수 있다.

<iframe width="560" height="315" src="https://www.youtube.com/embed/DguzKGCw-Qg" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<br>

Agent 들에게 게임을 수천번 플레이하게 하고 deep multi-agent reinforcement learning 을 통해 합리적으로 행동하도록 학습시켰다. 당연하게도 사과가 많은 환경에서는 agent 들이 평화롭게 공존하며 최대한 많은 사과를 수집했다. 하지만 사과의 갯수를 줄일 수록 agent 들은 상대 agent 를 "공격" 해서 부족한 사과를 독차지하며 수집하는 것이 유리함을 배우게 된다.

Gathering 게임은 원래 죄수의 딜레마와 많이 닮아 있는 것으로 나타났지만, 각 agent 들이 각자 적절한 행동을 만들어나가는 재미있는 과정을 연구할 수 있게 해준다: 공존하며 사과를 채집할 것인가, 상대 agent 를 공격할 것인가.

이러한 sequential social dilemmas 에서, 어떤 요소가 agent 들의 협력에 기여하는지를 확인할 수 있다. 예를 들어서, 다음 그래프는 Gathering 게임에서 사과가 부족할 수록 "공격" 이 더 자주 발생한다는 것을 보여준다. 더구나, 더 복잡한 전략을 세울 수 있는 agent 들일 수록 상대를 더 자주 공격하는, 즉 덜 협조적인 행동을 보인다. - 사과의 부족한 정도를 어떻게 바꾸어도 말이다.

<img src='../img/2020-09-27-Understanding_Agent_Cooperation_01.jpg' alt='fig.1' width=800 />


흥미롭게도, Wolfpack 이라고 하는 다른 게임에서는 (아래의 게임 영상 참조), 협력을 위해서 긴밀한 조화가 필요한데, 복잡한 전략을 짤 수 있는 능력 있는 agent 들이 agent 들 사이의 협력을 더 많이 이끌어냈고, 이는 Gathering 에서 발견한 것과는 반대의 결과이다. 그러므로, 환경에 따라서 복잡한 전략을 세울 수 있는 능력은 협력을 더 많이, 혹은 더 적게 만들 수도 있다. Sequential social dilemmas 라는 새로운 프레임웍은 상호 작용의 결과를 보여줄 뿐 아니라 (죄수의 딜레마에서 그랬듯이), 전략 수립을 학습하는 것이 얼마나 어려운지도 설명해준다.



<iframe width="560" height="315" src="https://www.youtube.com/embed/C0M1IXASXh4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


<br>
요약하자면, 우리는 deep multi-agent reinforcement leanring 이라는 현대적인 AI 기술을, 협력의 신비로운 발생과 같은 사회 과학의 오랜 문제에 적용할 수 있다. 학습된 AI agent 들은 경제학에서의 합리적인 agent 모델, 즉 호모 이코노미쿠스를 묘사한 것으로 생각할 수 있다. 따라서 이러한 모델들은, 정책적 개입을 상호작용하는 agent 들로 - 인간과 AI 모두 - 구성된 시뮬레이션 시스템에서 테스트할 수 있는 특별한 방법을 제공한다. 

결론적으로 우리는 경제, 교통 시스템, 지구의 생태 건전성과 같은 - 모두 우리의 협력에 달려 있는 - 복잡한 multi-agent 시스템을 더 잘 이해하고 운용할 수 있다.ㅣ