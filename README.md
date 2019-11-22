# University2-Data-Visualization
 * author 6O0b

 * see install.packages("rvest")

librayry(rvest)

CL_url <- 'http://www.op.gg/champion/statistics'
CL_html <- read_html(CL_url)
CL_class <- html_nodes(CL_html, '.champion-index__champion-list')


# 모든 챔피언 목록(Champion List) 불러오기 
champlist <- CL_class %>% html_nodes("a") %>% html_attrs()
champlist <- gsub("/champion/","",champlist)
champlist <- gsub("/statistics","",champlist)
champlist <- c(champlist[1:118],"trundle",champlist[119:145])
*  2019.11.22 기준 롤 챔피언 목록 (트런들은 표본이 너무 작아 빠져서 추가)


# 미드 라인을 가지 않는 챔피언 제외시키기 
Clist <- CL_class %>% html_children() %>% html_attrs()
Clist <- regexpr("MID",Clist)
champlist <- champlist[Clist!=-1]


# 챔피언의 미드 라인 플레이 세부 분석 url 불러오기
M_url1 <- 'http://www.op.gg/champion/'
M_url2 <- '/statistics/mid'
M_url <- paste0(M_url1, champlist, M_url2, sep='')


# 챔피언의 승률(Rate), 픽률(Pick), 티어(Tier), 주로 쓰는 핵심 룬(Rune)을 구해주는 함수
extract <- function(CM_url) {
  
    * 테스트 문장, 2019.11.22 기준 아트록스(Aatrox)라는 이름을 갖는 챔피언의 세부 데이터가 나온다.
    * CM_url <- M_url[1]

   *  챔피언의 미드 라인 플레이 세부 분석 정보 url의 html 정보 불러오기
    html <- read_html(CM_url)

   *  챔피언 이름 설정
   name <- html_nodes(html, '.champion-stats-header-info__name')%>% html_text()
   
 	*  승률(Rate), 픽률(Pick) 처리
   rp_class <- html_nodes(html, '.champion-stats-trend-rate')
   
   rate <- rp_class[[1]] %>% html_text()
   rate <- gsub("(\n)","",rate)
   rate <- gsub("(\t)","",rate)
   
   pick <- rp_class[[2]] %>% html_text()
   pick <- gsub("(\n)","",pick)
   pick <- gsub("(\t)","",pick)
  
   *  티어(Tier) 처리
   t_class <- html_node(html, '.champion-stats-header-info__tier')
   tier <- t_class %>% html_text()
   tier <- gsub("(\n)","",tier)
   tier <- gsub("(\t)","",tier)
   tier <- gsub(":","",tier)
   tier <- gsub(" ","",tier)
   tier <- gsub("Champion","",tier)
   tier <- gsub("Tier","",tier)
  
  *  핵심 룬(Rune) 처리
    * 룬의 계열(정밀/지배/마법/결의/영감) 구분하기 
      *     정밀  /  지배   일 경우 : 핵심 룬이 4개 
      *   마법 / 결의 / 영감일 경우 : 핵심 룬이 3개 
      * 핵심 룬의 계열을 불러와 구분한다.
      rl_class <- html_node(html, '.champion-stats-summary-rune__name')
      series <- substr(rl_class %>% html_text(),1,1)
      len <- 0
      ifelse(( series == 'P' || series == 'D' ), len <- 4, len <- 3)
    
   *  이미지 구분하기 
      *   op.gg에서는 보기 편하도록 같은 계열의 룬은 모두 나타내 준다.
      *   단, 쓰지 않는 룬은 흑백, 즉 grayscale 값이 있다.
      *   따라서 이미지 링크에 grayscale이 없는 룬을 찾아내야 한다.
      r_class <- html_nodes(html, '.perk-page__image')
      * len 수에 따라 반복 
      for(i in 1:len) {
        r_attrs <- r_class[[i+1]] %>% html_children() %>% html_attrs()
        * -1이  맞 다 => grayscale 이 없음 => 선택 o.
        * -1이 아니다 => grayscale 이 있음 => 선택 x.
        if( regexpr("grayscale", r_attrs)[[1]] == (-1) ) { used <- r_class[i+1] }
      }
      
   *  룬 선택하기 
      attr_used <- used %>% html_children() %>% html_attrs()
      index <- regexpr("color", attr_used)[[1]]
      rune <- substr(attr_used, index+16, index+20)

  *  결과 반환
    result <- c(name, rate, pick, tier, rune)
    return(result)
}

C_infor <- extract(M_url[1])
for(i in 2:length(champlist)) C_infor <- rbind(C_infor, extract(M_url[i]))
colnames(C_infor) <- c("챔피언 이름", "  승률", "   픽률", "   티어", "    룬")

C_infor

  * 핵심 룬 정보(Rune information)
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
