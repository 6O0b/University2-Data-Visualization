# Web Crawling in R with rvest
 * 6O0b

## 개요

프리시즌인 현재, '어떤 챔피언을 해야하는가', '각 챔피언 별로 어떤 룬을 들어야 하는가' 등등 고민이 많습니다.

따라서 게임의 가장 중심이 되는 미드라인에 가는 챔피언들의 승률, 픽률, 티어 그리고 어울리는 핵심 룬을 알아봅시다.

## 간략한 방법 설명

1. op.gg에서 미드 라인을 많이 선택하는 챔피언들의 목록을 모아줍니다.

2. 각 챔피언의 상세 정보를 담고 있는 페이지로 이동해 줍니다.

3. 챔피언의 이름, 승률 등 정보를 끌어와 정리합니다.

  ( 트런들은 표본이 너무 작아서 따로 추가했습니다. )

## 문제점

1. 데이터프레임으로 저장하여 숫자들의 비교가 가능하도록 해야합니다.

2. 승률, 픽률의 '%'를 없애주어야 합니다.

3. 얻을 수 있는 정보가 한정적인 것 같습니다.


## 핵심 룬 정보(Rune information)
  * 영문 표기( 한글표기 / 영문 줄임말 )
  * Precision(정밀/                               P)
  
     Press the Attack(집중공격/                  Press)
     
     Lethal Tempo(치명적 속도/                   Letha)
     
     Fleet Footwork(기민한 발놀림/               Fleet)
     
     Conqueror(정복자/                           Conqu)
  * Domination(지배/                              D)
  
     Electrocute(감전/                           Elect)
     
     Predator(포식자/                            Preda)
     
     Dark Harvest(어둠의 수확/                   Dark )
     
     Hail of Blades(칼날비/                      Hail )
  * Sorcery(마법/                                 S)
  
     Summon Aery(콩콩이 소환/                    Summo)
     
     Arcane Comet(신비로운 유성/                 Arcan)
     
     Phase Rush(난입/                            Phase)
  * Resolve(결의/                                 R)
  
     Grasp of the Undying(착취의 손아귀/         Grasp)
     
     Aftershock(여진/                            After)
     
     Guardian(수호자/                            Guard)
  * Inspiration(영감/                             I)
  
     Glacial Augment(빙결 강화/                  Glaci)
     
     Unsealed Spellbook(봉인 풀린 주문서/        Unsea)
     
     Prototype: Omnistone(프로토타입: 만능의 돌/ Proto)
