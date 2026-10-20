+++
title = "練習被拿走、語料被回填：工程判斷力與公共知識在哪裡失去自我修正"
date = "2026-10-03T06:55:04+08:00"
author = "么么陸"
draft = false
isCJKLanguage = true
description = "設想一位資深工程師看一眼錯誤日誌，就說「這是連線池被耗盡了」，而且多半是對的。她說不太清楚自己是怎麼看出來的，只知道這種日誌她看過很多次，其中幾次讓她熬了整夜。這種說不清楚、卻能可靠地引導判斷的知識，是工程組織最重要也最難傳遞的資產之一。本文討論的是：當這種知識的養成途徑，以及它所依賴的公共知識來源，都因為模型生成而改變時，組織會在哪裡失去發現自己錯誤的能力。"
tags = [
    "分析論述", # term:AnalyticalEssay
    "AI 經濟與社會", # term:AiEconomics
    "判斷", # term:Judgment
    "觀察", # term:Observation
    "默會知識", # term:TacitKnowledge
    "自動化反諷", # term:IroniesOfAutomation
    "模型坍塌", # term:ModelCollapse
    "幻覺包搶註寄生", # term:Slopsquatting
  ]
series = ["拆掉自己的煞車：組織如何在合理、合法甚至高效的名義下，失去發現自身錯誤的能力"]
term_exclude = ["Claim", "Hallucination", "Norm", "Screening"]
[ai_info]
    [ai_info.generation]
        model = "Claude Opus 5.5"
        agent = "GitHub Copilot Chat v0.68.0"
    [ai_info.refinement]
        model = "Gemini 3.8 Flash"
        agent = "Antigravity IDE 2.5.5"
+++

<!--more-->

## 導言

設想一位資深工程師看一眼錯誤日誌，就說「這是連線池被耗盡了」，而且多半是對的。她說不太清楚自己是怎麼看出來的，只知道這種日誌她看過很多次，其中幾次讓她熬了整夜。這種說不清楚、卻能可靠地引導**判斷**（Judgment） <!-- term:Judgment -->的知識，是工程組織最重要也最難傳遞的資產之一。本文討論的是：當這種知識的養成途徑，以及它所依賴的公共知識來源，都因為模型生成而改變時，組織會在哪裡失去發現自己錯誤的能力。

> [!IMPORTANT]
> **判斷** <!-- term:Judgment --> (Judgment): 面對規則未覆蓋或情境已改變時，根據脈絡評估決定的實質後果。 <!-- anchor:Judgment -->


本文要回答的問題是：當程式碼與解答越來越多由模型生成，判斷 <!-- term:Judgment -->越來越少由人親手練成時，一個工程組織，以及它所依賴的公共知識，會在哪裡失去自我修正的能力？

這個問題同時涉及兩個層次。在組織層次，它問的是：如果新人不再親手除錯，他們要從哪裡長出審查模型產出所需的判斷 <!-- term:Judgment -->力？在公共層次，它問的是：如果公開討論區的人類問答減少、網路上的文字越來越多由模型產生，下一代模型要從哪裡學到它還不會的東西？讀者若是帶領團隊的技術主管、設計培訓的工程師，或負責軟體供應鏈安全的人，讀完本文應能對自己的環境問三個問題：新人還有哪些機會在真實的失敗中練習判斷 <!-- term:Judgment -->？審查模型產出的人，具備的判斷 <!-- term:Judgment -->力從哪裡來？我們的工具與知識來源中，有多少比例來自模型自己的產出？

本文的論證分五步。第一步說明判斷 <!-- term:Judgment -->力如何形成，以及不用就會退化的證據。第二步用兩個航空與太空的現場，一個反例一個正例，說明「接手的那一刻」需要什麼。第三步整理近年關於人工智慧輔助的實證研究，指出一個反覆出現的型態：信心上升，準確度沒有跟上。第四步處理一個由模型錯誤直接打開的新攻擊面：不存在的套件名稱。第五步把同樣的邏輯推到公共知識層次，說明模型用自己的產出訓練時會失去什麼，以及什麼條件能避免。

證據的強度不一。航空與太空的事實取自官方事故報告與當事人口述；人工智慧輔助的研究多數是實驗室實驗、調查或**觀察**（Observation） <!-- term:Observation -->性研究，時間短、工具版本特定，不能直接推到所有情境。文中的重抽樣模型是本文設定，用來說明一個結構性質，不是對任何真實模型訓練的模擬。

> [!IMPORTANT]
> **觀察** <!-- term:Observation --> (Observation): 規格工作流中對指定環境、介面或系統版本現況進行檢查所獲得的事實紀錄。 <!-- anchor:Observation -->


## 分析

### 一、判斷力從哪裡來，又怎樣流失

哲學家 Michael Polanyi（1966）在《默會維度》中寫下一句被廣泛引用的話：「我們能知道的，多於我們能說出的。」他稱這種知識為**默會知識**（Tacit Knowledge） <!-- term:TacitKnowledge -->：騎腳踏車、辨認一張臉、在日誌中看出異常的那種能力，無法完整寫成規則，只能在實作中形成。

> [!IMPORTANT]
> **默會知識** <!-- term:TacitKnowledge --> (Tacit Knowledge): 無法完全言傳、只能在學徒制實踐與痛感反饋中內化的工程判斷。 <!-- anchor:TacitKnowledge -->


Dreyfus 兄弟在《心智勝於機器》中把技能的成長描述為從新手到專家的五個階段（Dreyfus & Dreyfus, 1986）。新手依賴明確的規則，專家則直接「看出」情境該怎麼處理，而跨越這些階段的途徑是大量處理真實情境的經驗。人類學家 Lave 與 Wenger（1991）在《情境學習》中提出合法周邊參與的概念：學習者在實踐社群中先承擔周邊但真實的工作，逐步走向完整參與。對軟體團隊而言，周邊但真實的工作，正是補單元測試、追蹤次要錯誤、清理舊程式碼這類任務。它們對公司的短期價值不高，卻是新人接觸系統真實行為的入口。

本文由此推出一個需要被檢查的主張：如果這些周邊任務被模型接手，新人不只是少做了一些雜事，而是少了一條長出判斷 <!-- term:Judgment -->力的路。這是本文推論，目前沒有直接追蹤軟體工程師長期技能發展的研究可以證實或否定它。

能部分支持這個推論的，是關於技能退化的研究。Arthur 等人（1998）彙整了技能保持的實證研究，發現技能在停止使用後會流失，而認知性、精確度導向的任務比體能性、速度導向的任務流失得更多；停用超過一年後，流失的效果量可達 $d = -1.4$。對已經具備技能的工程師，這意味著不常使用的除錯能力會退化；對從未練成的人，問題更根本，因為沒有可以退化的東西。

心理學家 Lisanne Bainbridge（1983）在〈自動化的反諷〉中把這個困境說得最清楚。她指出兩點：自動化越先進，人在少數需要接手的時刻所做的貢獻就越關鍵；但「身體技能在不使用時會退化」，平時被自動化接管，人也就失去了維持接手能力的機會。她的建議之一是必須提供模擬器練習。本文把這個困境稱為**自動化反諷**（Ironies Of Automation） <!-- term:IroniesOfAutomation -->，它是下一節兩個現場的共同背景。

> [!IMPORTANT]
> **自動化反諷** <!-- term:IroniesOfAutomation --> (Ironies Of Automation): 自動化接手例行工作後，留給人的案例更難且練習更少，反而更難在關鍵時刻接管的設計張力。 <!-- anchor:IroniesOfAutomation -->


### 二、接手的那一刻：兩個現場

#### 反例：法航 447 號班機

2009 年 6 月 1 日，法航 447 號班機從里約熱內盧飛往巴黎，在大西洋上空墜毀，機上 12 名機組員與 216 名乘客全數罹難。法國航空事故調查局在 2012 年的最終報告中重建了事件經過（Bureau d'Enquêtes et d'Analyses pour la sécurité de l'aviation civile [BEA], 2012）。

協調世界時 02:10:05，空速量測出現暫時的不一致，報告認為「可能是空速管被冰晶堵塞」所致。自動駕駛與自動油門隨即斷開，飛行控制律從正常模式降為備用模式，原本防止失速的保護不再完整。操縱飛機的副駕駛做出「機頭向上並向左」的操作。飛機隨後進入失速，攻角「超過 40 度」，失速警告曾連續響了 54 秒。機組員始終沒有辨識出飛機處於失速狀態。02:14:28，飛機撞擊海面，距自動駕駛斷開約 4 分 23 秒。

報告在訓練方面指出，機組員缺乏「在高空以手動操縱飛機的任何訓練」，副駕駛們也沒有接受過高空接近失速與失速改出的手動操縱訓練。這不等於他們從未接受任何失速相關的訓練，而是他們沒有在與事故相同的條件下練習過。

這個個案支持的是 Bainbridge 的反諷：自動化在 02:10 把飛機交還給人，交還的時刻正是最困難、也最少練習的時刻。它不支持一個更強的說法，即事故純粹由「依賴自動化導致的技能退化」造成。報告列出的因素還包括座艙警示的設計、失速警告在某些條件下會停止的邏輯、機組員之間的協調等。美國聯邦航空總署在 2013 年發布安全通報，鼓勵航空業者「在適當時機推動手動飛行操作」，回應的正是手動技能在高度自動化下的維持問題（Federal Aviation Administration, 2013）。

#### 正例：阿波羅 11 號的 1201 與 1202 警報

1969 年 7 月，阿波羅 11 號的登月小艇在下降途中，電腦接連發出 1201 與 1202 程式警報。1201 表示「沒有可用的向量累加區」，1202 表示「沒有可用的核心集」，兩者都意味著電腦的工作排程資源被用盡。原因是交會雷達與電腦之間的一個介面同步問題，佔用了約 13% 的運算時間。參與飛行軟體開發的 Don Eyles 指出，當時雷達開關的設定「本不應該造成任何問題」，因此不能把它簡化成太空人撥錯開關（Eyles, 2004）。

電腦沒有崩潰，是因為它的作業排程系統設計了優先順序與重新啟動保護：資源用盡時，系統重新啟動，只重建必要的工作，丟棄次要工作。Eyles 把這套排程系統的設計歸功於 Hal Laning。Margaret Hamilton 是開發飛行軟體團隊的負責人，NASA 稱她為「開發飛行軟體團隊的領導者」（NASA, 2003）；這與排程系統由誰設計是兩件事，常見的說法把兩者混為一談。

地面上，負責判斷 <!-- term:Judgment -->警報是否要中止登月的工程團隊，在第一次警報後約半分鐘內給出繼續的指示（Eyles, 2004）。他們能這麼快，是因為在先前的模擬中，一次因程式警報而錯誤中止的演練，促使團隊整理了每個警報的處置方式。當時參與地面支援的工程師 Jack Garman 在口述歷史中描述，那份清單是「手寫的，壓在一片塑膠下面」（Garman, 2001）。

兩個現場的對照，指向同一個條件。在接手的那一刻，有用的不是平時是否使用自動化，而是人是否曾在接近真實的條件下，練習過自動化失效時的判斷 <!-- term:Judgment -->。阿波羅的地面團隊高度依賴電腦，但他們刻意演練了電腦出錯的情境；法航的機組員也受過訓練，但沒有練過那一刻的情境。

### 三、人工智慧輔助的證據：四種不同的觀察

近年有幾項研究，從不同角度觀察 <!-- term:Observation -->人工智慧輔助對工作品質與判斷 <!-- term:Judgment -->的影響。它們測量的東西不同：有的測產出的安全性，有的測完成時間與當事人的估計，有的測自陳的認知投入，有的測不使用輔助時的技能表現。它們的方法、對象與工具版本也都不同，不能合成一個結論。下面分開列出，再說明它們能共同支持什麼。

在程式安全方面，Pearce 等人（2022）讓 GitHub Copilot 在 89 個與資安相關的情境中產生 1,689 個程式，發現其中約 40% 含有弱點。這些情境是刻意挑選的資安情境，不代表一般程式碼的弱點比例。Perry 等人（2023）進一步做了使用者實驗：使用 AI 助理的參與者寫出的程式「明顯較不安全」，卻「更傾向相信自己寫出了安全的程式」。

在生產力方面，METR 在 2025 年進行了一項隨機對照實驗，讓 16 位資深開源開發者在自己熟悉的成熟專案中完成 246 項任務。開發者事前預期使用 AI 工具能縮短 24% 的時間，事後估計縮短了 20%，實際測量的結果卻是使用 AI 工具時花的時間多了 19%（Becker et al., 2025）。這項研究的工具是 2025 年初的版本，參與者是熟悉專案的資深開發者，結果不能推廣到新手或後來的工具。

在判斷 <!-- term:Judgment -->的投入方面，Lee 等人（2025）調查了 319 位知識工作者，發現對生成式 AI 的信心越高，自述投入的批判性思考越少。這是自陳資料的相關，不能證明批判思考能力下降。

在專業技能方面，一項多中心觀察 <!-- term:Observation -->研究比較了內視鏡醫師在引入 AI 輔助前後，不使用 AI 時的表現：腺瘤偵測率從 28.4% 降到 22.4%（Budzyń et al., 2025）。這是觀察 <!-- term:Observation -->性的前後比較，不是隨機實驗，但它是少數直接觀察 <!-- term:Observation -->到「用過輔助之後，不用輔助時表現下降」的研究。

直接比較「人的判斷 <!-- term:Judgment -->」與「實際結果」的只有兩項：Perry 等人的實驗中，參與者對程式安全性的信心與實際安全性背道而馳；METR 的實驗中，開發者對時間節省的估計與實測方向相反。在這兩項研究裡，可以說出現了**校準**（Calibration） <!-- term:Calibration -->的落差，也就是人對自己工作的評估與實際結果之間的距離變大了。其餘兩項研究支持的是較間接的方向：自陳的批判投入隨信心降低，以及不使用輔助時的表現下降。這對組織有一個含義。許多團隊的做法是讓模型產生程式碼，再由人審查。但審查程式碼是否安全、是否正確，需要的正是第一節所說的判斷 <!-- term:Judgment -->力，而那種判斷 <!-- term:Judgment -->力是在親手寫、親手除錯中長出來的。如果新人一開始就只做審查，他缺少的不是勤奮，而是審查所需的經驗；如果審查者對自己判斷 <!-- term:Judgment -->的信心又因為輔助而上升，問題就更難被發現。這是本文推論，它把上述研究與第一節的技能形成理論連起來，尚無直接檢驗這條完整因果鏈的研究。

> [!IMPORTANT]
> **校準** <!-- term:Calibration --> (Calibration): 模型輸出機率與實際正確率的一致程度。 <!-- anchor:Calibration -->


判斷 <!-- term:Judgment -->力具體練的是什麼，可以舉例說明。本文設定的例子包括：讀懂一段很長的呼叫堆疊，推回程式崩潰前的狀態；設中斷點、寫一個會失敗的反例測試，用觀察 <!-- term:Observation -->排除錯誤的假設；辨認一個「讓錯誤訊息消失」的修補，例如捕捉所有例外或回傳預設值，其實只是把資料損壞藏起來。這些能力的共同點是：它們只能在面對真實或高度擬真的失效情境時練成，也正是審查模型產出時最需要的能力。資深工程師退休或轉任管理職時，如果沒有人在他身邊一起處理過這些錯誤，這些能力就跟著離開。

審查要擋下的錯誤長什麼樣子，也值得說具體。Pearce 等人（2022）的測試情境中，有一部分取自 MITRE「2021 年最危險的 25 種軟體弱點」清單中的若干項，例如 SQL 注入與越界寫入；其餘情境則用來測試提示措辭的變化與硬體設計的弱點。本文另舉三類不容易在順利路徑的測試中顯現的錯誤：多個執行緒同時存取資料時才會出現的競態條件；錯誤處理分支裡沒有釋放的檔案、連線等資源；會被特定輸入拖到指數時間的正規表示式。這三類是本文舉例，不是研究測得的比例。它們的共同點是：程式碼看起來整齊，基本測試也可能通過，只有知道要去哪裡找的人才找得到。

一個常見的延伸說法認為，模型生成的程式碼在程式庫中像放射性物質一樣長期釋放隱患，排查成本可達手寫的數十倍。這個說法沒有實證數字支持，本文不採用。可以確定的是一個較弱的結構性質：一段程式碼若沒有任何人理解它為什麼這樣寫，它出錯時的排查就只能從零開始，而這與程式碼是誰寫的無關，與是否有人理解它有關。本文建議，對生成的程式碼至少留下三件事的紀錄：它要解決什麼問題、為什麼選這個做法、哪些情況刻意沒有處理，讓日後的除錯有起點。

### 四、一個新的入口：不存在的套件

模型的錯誤有一種會直接變成資安問題。程式碼生成模型有時會在程式中引用一個根本不存在的套件，這稱為**幻覺依賴**（Hallucinated Dependencies） <!-- term:HallucinatedDependencies -->。如果攻擊者事先在公開的套件庫註冊了這個名稱，並在裡面放入惡意程式，照著模型建議安裝套件的開發者就會把惡意程式帶進專案。惡意程式甚至不必等到被呼叫：以 npm 為例，安裝套件時會依序執行套件宣告的 preinstall、install、postinstall 腳本，除非使用者設定略過腳本（npm, n.d.）。所以在照著建議執行安裝指令的那一刻，攻擊就可能已經完成，早於任何人審查這個套件在程式中怎麼被使用。這種攻擊被稱為**幻覺包搶註寄生**（Slopsquatting） <!-- term:Slopsquatting -->，名稱由 Python 軟體基金會的資安駐點開發者 Seth Larson 在一次討論中提出（Nesbitt, 2025）。

> [!IMPORTANT]
> **幻覺依賴** <!-- term:HallucinatedDependencies --> (Hallucinated Dependencies): 模型自信呼叫不存在的套件名稱，為幽靈包搶註與供應鏈投毒打開入口。 <!-- anchor:HallucinatedDependencies -->
> **幻覺包搶註寄生** <!-- term:Slopsquatting --> (Slopsquatting): 攻擊者搶先註冊模型常幻覺出的套件名，藉安裝路徑植入惡意程式碼。 <!-- anchor:Slopsquatting -->


Spracklen 等人（2025）對這個現象做了大規模的測量。在他們測試的模型中，建議的套件裡不存在的平均比例，商業模型至少 5.2%，開源模型達 21.7%；總共出現 205,474 個不同的虛構套件名稱。更關鍵的是可預測性：在一項子實驗中，研究者從先前曾產生虛構套件的提示中抽樣 500 個，對四個模型各重新查詢十次，43% 的虛構名稱十次都重複出現。可預測，就意味著可以被預先搶註。

這種攻擊是否已被大規模利用，公開證據有限，但**概念驗證**（Proof Of Concept） <!-- term:ProofOfConcept -->已經存在。資安研究者 Bar Lanyado 在 2024 年把一個模型經常虛構的名稱 huggingface-cli 註冊為一個空的套件，三個月內獲得超過 3 萬次他判定為真實的下載（Lanyado, 2024）。

> [!IMPORTANT]
> **概念驗證** <!-- term:ProofOfConcept --> (Proof Of Concept): 在規模化採購前驗證技術可行性與邊界條件的試驗。 <!-- anchor:ProofOfConcept -->


這個問題與前一節的判斷 <!-- term:Judgment -->力問題相連。一位熟悉生態系的工程師看到陌生的套件名稱，會先查它是否存在、由誰維護、有多少人使用；一位只負責「讓程式跑起來」的人，看到安裝指令就會照著執行。防禦的技術手段並不複雜，例如只允許安裝清單上的套件、鎖定版本、對新出現或極少下載的套件發出警示，這些是本文建議的常見做法。但手段要被設定與維護，仍需要有人知道為什麼需要它們。

### 五、公共知識的回填：模型用自己的產出訓練時會失去什麼

前面幾節談的是組織內部的判斷 <!-- term:Judgment -->力。同樣的邏輯可以推到公共層次：如果新的訓練資料越來越多來自模型自己的產出，模型會怎樣？

Shumailov 等人（2024）在《自然》期刊上發表的研究，用理論與實驗說明了**模型坍塌**（Model Collapse） <!-- term:ModelCollapse -->：當每一代模型都用前一代模型產生的資料訓練時，會先發生「早期坍塌」，模型開始失去分布尾端的資訊，也就是罕見但真實的情況；繼續下去會發生「晚期坍塌」，模型收斂到一個與原始分布相去甚遠、「變異常常大幅縮小」的分布。注意，論文描述的是變異「常常」大幅縮小、輸出趨同，不是變異發散。這說的是單一路徑上模型分布的集中；若把同樣的過程獨立重複許多次，每一次可能集中到不同的地方，跨重複之間的差異反而會變大（本文說明）。

> [!IMPORTANT]
> **模型坍塌** <!-- term:ModelCollapse --> (Model Collapse): 遞歸訓練合成數據導致長尾滅絕、方差發散，分佈不可逆退化為奇異點或噪聲。 <!-- anchor:ModelCollapse -->


這個結論有一個重要的前提：每一代的訓練資料都被前一代的產出取代。研究中的語言模型實驗也顯示，每一代保留 10% 的原始資料，退化就變得輕微。Gerstgrasser 等人（2024）則研究了另一種情形：每一代的合成資料被加到既有資料上累積，而不是取代既有資料。在他們分析的線性模型框架中，測試誤差有一個與世代數無關的上限；他們在多種生成模型上的實驗也支持這個方向。這不是說累積能保證不損失任何性質，但它說明了模型坍塌 <!-- term:ModelCollapse -->不是無條件發生的，它取決於真實資料是否被保留。

為什麼取代會讓罕見情況消失？下面的模型用最簡單的形式說明這個結構。本文設定：一個資料池裡有幾種類別，其中一種很罕見；每一代從目前的資料池中隨機抽樣，產生新的資料。「取代」模式下，新資料取代整個資料池；「累積」模式下，新資料加入資料池，原始資料始終保留。

```python
# 本文設定：類別與數量皆為設想；用來說明重抽樣的結構性質，不模擬任何真實模型訓練
import random

ORIGINAL = ["common"] * 95 + ["uncommon"] * 4 + ["rare"] * 1

def run(mode, generations=200, sample_size=100, seed=0):
    rng = random.Random(seed)
    pool = list(ORIGINAL)
    rare_counts = []
    for _ in range(generations):
        new = [rng.choice(pool) for _ in range(sample_size)]
        pool = new if mode == "replace" else pool + new
        rare_counts.append(pool.count("rare"))
    return rare_counts

lost_in_some_run = False
for seed in range(20):
    replaced = run("replace", seed=seed)
    # 取代模式：罕見類別一旦從資料池消失，之後永遠不會再出現
    if 0 in replaced:
        lost_in_some_run = True
        first_loss = replaced.index(0)
        assert all(c == 0 for c in replaced[first_loss:])
    # 累積模式：原始資料始終在池中，罕見類別永遠不會歸零
    assert all(c >= 1 for c in run("accumulate", seed=seed))
assert lost_in_some_run   # 取代模式下，消失確實會發生，上面的吸收檢查不是空轉
```

前兩個斷言檢查的是結構，不依賴隨機種子的運氣；最後一個斷言確認在所列的二十個種子中，取代模式下罕見類別至少消失過一次，所以吸收的檢查不是空轉。在取代模式下，每一代只能從上一代的資料中抽樣，一個類別一旦在某一代沒有被抽到，資料池裡就再也沒有它，之後也不可能再抽到。這與族群遺傳學中的 Wright-Fisher 模型相同：在沒有突變與遷入的有限族群中，一個等位基因一旦消失，就無法再出現（Marcus, 2016）。在累積模式下，原始資料一直在池中，罕見類別至少保有原始的那一筆。但要注意，「還在池中」不等於「還被學到」：資料池持續變大，罕見類別所占的比例仍會被稀釋，訓練時若抽樣不到，它對模型的影響一樣會變小。保留資料是必要條件，不是充分條件。

這個模型的限制很多。真實的模型訓練不是單純的重抽樣，模型會在資料之間插值，也可能產生原始資料中沒有的組合；真實的資料管線也會過濾與混合資料。模型只說明一件事：當新資料只能來自舊資料的抽樣時，失去的東西沒有回來的路，除非有新的、外部的輸入。

公共知識正面臨的，是另一個相關但不同的問題。del Rio-Chanona 等人（2024）比較了 ChatGPT 發布前後的程式問答網站活動，發現在發布後六個月內，Stack Overflow 的活動相對於無法使用 ChatGPT 的俄語與中文對照平台，下降了 25%。這是**反事實**（Counterfactual） <!-- term:Counterfactual -->比較的觀察 <!-- term:Observation -->性估計，不是直接的因果實驗，但它指出一個方向：人們把問題拿去問模型，公開的人類問答就少了。這裡要分開兩件事。保留既有的真實資料，就足以避免模型在原有知識上坍塌，這是 Gerstgrasser 等人的結論；但新的技術、新的錯誤型態與新的問題，只能從新產生的人類經驗中學到，舊資料保留得再好也提供不了。公開問答減少，侵蝕的是後者。本文把這兩件事連起來：公開的人類知識減少、網路上的合成文本增加，會讓模型越來越難學到它還不會的東西。這是本文推論，實際影響取決於模型開發者如何篩選與保留資料，也取決於維護公共知識的人是否還有動機繼續公開分享。

> [!IMPORTANT]
> **反事實** <!-- term:Counterfactual --> (Counterfactual): 在未實際發生的處置下本應出現的結果，是因果宣稱的基準，也是觀測資料中永遠缺失的那一半。 <!-- anchor:Counterfactual -->


維護者還承受另一種壓力。Seth Larson 在 2024 年底指出，開源專案收到的「極低品質、垃圾般、由**大型語言模型**（Large Language Model） <!-- term:LargeLanguageModel -->幻覺產生」的資安報告明顯增加（Larson, 2024）；curl 的維護者 Daniel Stenberg 在 2025 年形容他們「實際上正在被 DDoS」（Willison, 2025 轉引）。每一份這樣的報告，都要由人花時間判斷 <!-- term:Judgment -->真偽。依本文推論，當維護者的時間被這類報告消耗，而公開問答的參與又在減少，公共知識的產生與維護會同時承壓；這是資助與治理的問題，不只是技術問題。

> [!IMPORTANT]
> **大型語言模型** <!-- term:LargeLanguageModel --> (Large Language Model): 基於海量文本數據訓練的深層神經網路模型，用於處理、生成和理解自然語言 <!-- anchor:LargeLanguageModel -->


本文把這種趨勢的極端形態稱為**認識論荒漠化**（Epistemic Desertification） <!-- term:EpistemicDesertification -->，這是本文採用的比喻，不是研究術語。比喻抓住了方向，但容易誇大：本節的研究顯示，模型坍塌 <!-- term:ModelCollapse -->可以透過保留與累積真實資料來減輕，它不是熱力學定律式的必然。

> [!IMPORTANT]
> **認識論荒漠化** <!-- term:EpistemicDesertification --> (Epistemic Desertification): 自動化切斷人類與底層錯誤的摩擦後，默會知識與除錯直覺代際失傳、知識土壤趨於均質貧瘠的認識論退化。 <!-- anchor:EpistemicDesertification -->


### 六、讓模型審查模型：驗證的同源問題

最後一個問題把組織與公共兩個層次接在一起。當程式碼由模型生成、測試由模型撰寫、審查由模型執行、事後檢討由模型摘要時，每一道檢查看起來都在，但它們的錯誤可能高度相關：同一類模型在同一類問題上的盲點，會同時出現在生成與檢查中。

一道檢查能擋下錯誤，前提是它的錯誤與被檢查對象的錯誤不同。用同一類來源產生答案與檢查答案，兩者可能共享同樣的盲點，這時多加幾道檢查，增加的保障比表面上少。這不表示同源的檢查毫無用處：Spracklen 等人也發現，讓模型檢查自己的輸出，能辨識出一部分虛構的套件名稱；而由模型撰寫的測試若交給編譯器與真實的執行環境去跑，執行結果本身就是一個不依賴模型判斷 <!-- term:Judgment -->的判準。判斷 <!-- term:Judgment -->一道檢查是否獨立，要看的不是誰寫了它，而是它的判準從哪裡來：是外部的規格、真實的執行結果、人的獨立判斷 <!-- term:Judgment -->，還是與被檢查對象同一個來源的判斷 <!-- term:Judgment -->。這是本文推論，目前沒有直接測量「模型審查模型」錯誤相關性的大規模研究。

## 反思

**證據多是短期、特定的。** 第三節的研究，時間跨度從幾週到幾個月，工具版本各不相同，研究設計包含實驗、調查與觀察 <!-- term:Observation -->。METR 的結果只適用於熟悉專案的資深開發者與 2025 年初的工具；內視鏡研究是觀察 <!-- term:Observation -->性的；知識工作者的研究是自陳。它們共同指出值得擔心的方向，但不能告訴我們技能退化的速度與規模。本文的組織層次推論，需要追蹤工程師數年發展的研究才能檢驗。

**技能會轉移，不只會流失。** 每一次工具的變化都會讓一些技能變得不重要，同時讓另一些技能變得重要。計算機普及後，很少人還需要心算長除法，這通常不被視為損失。本文的論證不主張所有被自動化的技能都需要保留，它主張的是較窄的事：在自動化會失效、而失效時需要人接手的地方，接手所需的判斷 <!-- term:Judgment -->力必須在某處被練成。

**自動化與演練可以並存。** 阿波羅的例子說明，高度依賴電腦的團隊仍可以具備在電腦出錯時判斷 <!-- term:Judgment -->的能力，條件是刻意演練失效情境。這意味著解方不是減少使用工具，而是把練習失效設計進工作中。本文也不主張高度自動化讓飛行變得更不安全：兩個現場處理的只是自動化交還控制的那一刻，不是自動化的整體效益。

**模型坍塌 <!-- term:ModelCollapse -->不是必然。** 模型坍塌 <!-- term:ModelCollapse -->的研究常被引用為「AI 終將自我毀滅」的證據，但研究本身顯示，保留真實資料就能大幅減輕模型坍塌 <!-- term:ModelCollapse -->。真正的問題是經濟與制度上的：真實資料的產生者是否還有動機繼續產生並公開它們，以及模型開發者是否有能力分辨真實與合成的資料。

**幻覺依賴 <!-- term:HallucinatedDependencies -->的規模仍不清楚。** Spracklen 等人測量的是模型產生虛構名稱的頻率與可預測性，Lanyado 的實驗證明了攻擊的可行性。兩者都不等於已有大規模的實際攻擊。本文把它列為已被證明可行、值得預防的風險，而不是已經普遍發生的事件。

## 實務對比

面對「模型能產生大部分程式碼」的現實，團隊可以採取不同的組織方式。下表比較三種做法在本文三個檢查問題上的表現。第三欄是本文建議，結合了 Bainbridge 的模擬練習建議與航空業對手動操作的實務，尚無軟體團隊的對照研究支持其效果。

| 檢查問題 | 全面替代 | 生成後人工審查 | 刻意保留練習（本文建議） |
| :--- | :--- | :--- | :--- |
| 新人在哪裡練成判斷 <!-- term:Judgment --> | 沒有固定的地方 | 從審查中學，但審查需要他尚未具備的判斷 <!-- term:Judgment --> | 保留一部分真實的周邊任務由人親手完成；定期進行不使用輔助的故障排除演練 |
| 審查者的判斷 <!-- term:Judgment -->力從哪裡來 | 不適用 | 依賴資深者，而資深者的技能因少用而退化 | 審查時要求說明「為什麼這段程式碼是對的」，而非只確認測試通過 |
| 獨立檢查是否存在 | 生成、測試、審查的判準同源 | 人工審查常是主要的獨立判準 | 至少一道檢查的判準來自生成來源以外，例如人工撰寫的關鍵測試、**外部規格**（Specifications） <!-- term:Specifications -->或真實執行結果 |
| 經驗如何傳遞 | 不刻意安排 | 資深者審查時順帶指點 | 資深者與新人一起處理真實故障，邊做邊說出推理；績效評估納入排除深層問題與傳授經驗，而非只看產出量 |
| 依賴是否被核實 | 照模型建議安裝 | 依審查者是否留意 | 套件允許清單、版本鎖定、新套件的自動警示 |
| 交付後誰能維護 | 只有生成工具 | 當初的審查者 | 契約或內部規範要求在不使用生成工具時也能維護核心邏輯，並保留設計**意圖**（Intent） <!-- term:Intent -->的紀錄 |

> [!IMPORTANT]
> **外部規格** <!-- term:Specifications --> (Specifications): 由外部團隊提供的權威性系統規格文件 <!-- anchor:Specifications -->
> **意圖** <!-- term:Intent --> (Intent): 具名需求來源對系統預期功能或業務價值所表達的主觀期待與目標。 <!-- anchor:Intent -->


對應到公共知識層次，模型開發者與知識社群也面對類似的選擇。下表依第五節的研究整理兩種資料策略的差別。

| 對照項 | 取代：以新產出取代舊資料 | 累積：保留真實資料並加入新產出 |
| :--- | :--- | :--- |
| 罕見情況 | 一旦在某一代沒有被抽到，就無法回來 | 至少保有原始資料中的那些 |
| 研究結果 | 早期失去尾端，晚期變異大幅縮小 | 在分析的框架中，誤差有與世代無關的上限 |
| 依賴的條件 | 不需要保留原始資料 | 需要能辨識並保留真實資料；要學到新的東西，還需要新的真實資料 |

第二張表的右欄，把問題從技術轉向制度：保留既有資料能守住原有的知識，但學到新知識需要新的人類經驗被持續公開。公開問答的減少，侵蝕的是後者。

## 結論

工程判斷 <!-- term:Judgment -->力是在親手處理真實失敗的過程中長出來的，不用就會退化，沒練過就無從退化。依本文推論，當模型接手了新人過去用來練習的周邊任務，組織就可能少了一條長出判斷 <!-- term:Judgment -->力的路；當審查模型產出的工作被交給這樣的新人，審查所需的判斷 <!-- term:Judgment -->就可能成為缺口。近年的研究從不同角度指出，使用輔助時，人對自己工作的評估與實際結果之間可能出現落差，不使用輔助時的表現也可能下降。

在接手的那一刻，關鍵不在於平時是否使用自動化，而在於是否在接近真實的條件下練習過自動化失效的情境。這是法航 447 與阿波羅 11 號兩個現場的共同教訓，也是 Bainbridge 在四十多年前的建議。

公共知識面臨結構相同的問題。模型用自己的產出取代真實資料來訓練時，罕見而真實的情況會先消失，而且沒有回來的路；保留並累積真實資料可以大幅減輕這種模型坍塌 <!-- term:ModelCollapse -->；但要讓模型學到它還不會的東西，還需要新的人類經驗持續被公開。公開的人類問答正在減少，這是需要被注意的訊號，不是已經確定的結局。

可帶走的檢查有三個問題：新人還有哪些機會在真實的失敗中練習判斷 <!-- term:Judgment -->？審查模型產出的人，判斷 <!-- term:Judgment -->力從哪裡來，有沒有一道檢查的判準不來自生成者？我們依賴的工具與知識來源中，有多少是由模型自己的產出回填的？

## 參考文獻 (References)

文中以（作者, 年份）標示出處，條目依作者字母排序。讀取日期為 2026-10-03。本文的重抽樣模型與表中「本文建議」欄為設定或建議，不是實測。實證研究的結論以各自的研究設計、對象與工具版本為限。

- Arthur, W., Jr., Bennett, W., Jr., Stanush, P. L., & McNelly, T. L. (1998). Factors that influence skill decay and retention: A quantitative review and analysis. *Human Performance, 11*(1), 57–101. [https://doi.org/10.1207/s15327043hup1101_3](https://doi.org/10.1207/s15327043hup1101_3)
- Bainbridge, L. (1983). Ironies of automation. *Automatica, 19*(6), 775–779. [https://doi.org/10.1016/0005-1098(83)90046-8](https://doi.org/10.1016/0005-1098(83)90046-8)
- Becker, J., Rush, N., Barnes, E., & Rein, D. (2025). *Measuring the impact of early-2025 AI on experienced open-source developer productivity* (arXiv:2507.09089). METR. [https://arxiv.org/abs/2507.09089](https://arxiv.org/abs/2507.09089)
- Budzyń, K., Romańczyk, M., Kitala, D., Kołodziej, P., Bugajski, M., Adami, H. O., Blom, J., Buszkiewicz, M., Halvorsen, N., Hassan, C., Romańczyk, T., Holme, Ø., Jarus, K., Fielding, S., Kunar, M., Pellise, M., Pilonis, N., Kamiński, M. F., Kalager, M., Bretthauer, M., & Mori, Y. (2025). Endoscopist deskilling risk after exposure to artificial intelligence in colonoscopy: A multicentre, observational study. *The Lancet Gastroenterology & Hepatology, 10*(10), 896–903. [https://doi.org/10.1016/S2468-1253(25)00133-5](https://doi.org/10.1016/S2468-1253(25)00133-5)。本次讀取摘要與書目資料。
- Bureau d'Enquêtes et d'Analyses pour la sécurité de l'aviation civile. (2012, July). *Final report on the accident on 1st June 2009 to the Airbus A330-203 registered F-GZCP operated by Air France flight AF 447 Rio de Janeiro – Paris*. [官方報告 PDF](https://bea.aero/fileadmin/documents/docspa/2009/f-cp090601.en/pdf/f-cp090601.en.pdf)。文中簡稱 BEA, 2012；引用範圍：§§1.1–1.2、3.1–3.2、4.1.1。
- del Rio-Chanona, R. M., Laurentsyeva, N., & Wachs, J. (2024). Large language models reduce public knowledge sharing on online Q&A platforms. *PNAS Nexus, 3*(9), pgae400. [https://doi.org/10.1093/pnasnexus/pgae400](https://doi.org/10.1093/pnasnexus/pgae400)。本次讀取摘要。
- Dreyfus, H. L., & Dreyfus, S. E. (1986). *Mind over machine: The power of human intuition and expertise in the era of the computer*. Free Press. ISBN 9780029080603。本次只核對書目與目錄。
- Eyles, D. (2004). *Tales from the lunar module guidance computer* (AAS 04-064). 27th Annual AAS Guidance and Control Conference. [作者網頁](https://www.doneyles.com/LM/Tales.html)
- Federal Aviation Administration. (2013, January 4). *Manual flight operations* (SAFO 13002). [官方 PDF](https://www.faa.gov/sites/faa.gov/files/other_visit/aviation_industry/airline_operators/airline_safety/SAFO13002.pdf)
- Garman, J. R. (2001, March 27). *Oral history transcript* (K. M. Rusnak, Interviewer). NASA Johnson Space Center Oral History Project. [官方 PDF](https://www.nasa.gov/wp-content/uploads/2025/07/garmanjr-3-27-01.pdf)。引用範圍：pp. 21–24。
- Gerstgrasser, M., Schaeffer, R., Dey, A., Rafailov, R., Sleight, H., Hughes, J., Korbak, T., Agrawal, R., et al. (2024). Is model collapse inevitable? Breaking the curse of recursion by accumulating real and synthetic data. In *Conference on Language Modeling (COLM 2024)*. [https://arxiv.org/abs/2404.01413](https://arxiv.org/abs/2404.01413)。作者順序依 arXiv v2；完整作者名單見原文。
- Lanyado, B. (2024, March 28). *Diving deeper into AI package hallucinations*. Lasso Security. [官方網頁](https://www.lasso.security/blog/ai-package-hallucinations)。下載次數為作者以對照套件判定的「真實」下載，非獨立稽核數字。
- Larson, S. (2024, December 3). *New era of slop security reports for open source*. [作者網頁](https://sethmlarson.dev/slop-security-reports)
- Lave, J., & Wenger, E. (1991). *Situated learning: Legitimate peripheral participation*. Cambridge University Press. ISBN 9780521423748
- Lee, H.-P., Sarkar, A., Tankelevitch, L., Drosos, I., Rintel, S., Banks, R., & Wilson, N. (2025). The impact of generative AI on critical thinking: Self-reported reductions in cognitive effort and confidence effects from a survey of knowledge workers. In *Proceedings of the 2025 CHI Conference on Human Factors in Computing Systems* (Article 1121). [https://doi.org/10.1145/3706598.3713778](https://doi.org/10.1145/3706598.3713778)
- Marcus, J. (2016, March 29). *Introduction to the Wright-Fisher model*. fiveMinuteStats. [網頁](https://stephens999.github.io/fiveMinuteStats/wright_fisher_model.html)。教學資源；引用範圍：無突變與遷入時，等位基因的固定與消失是吸收狀態。
- NASA. (2003, September 3). *NASA honors Apollo engineer* [News release]. [Apollo Lunar Surface Journal 保存版](https://apollojournals.org/alsj/a11/a11Hamilton.html)
- Nesbitt, A. (2025, April 8). [Post defining "slopsquatting"]. Mastodon. [原始貼文](https://mastodon.social/@andrewnez/114302875075999244)。引用範圍：名稱歸功於 Seth Larson。
- npm. (n.d.). *Scripts* (npm CLI v11 documentation). [官方文件](https://docs.npmjs.com/cli/v11/using-npm/scripts)。引用範圍：npm install 時的生命週期腳本順序。
- Pearce, H., Ahmad, B., Tan, B., Dolan-Gavitt, B., & Karri, R. (2022). Asleep at the keyboard? Assessing the security of GitHub Copilot's code contributions. In *2022 IEEE Symposium on Security and Privacy* (pp. 754–768). [https://doi.org/10.1109/SP46214.2022.9833571](https://doi.org/10.1109/SP46214.2022.9833571)
- Perry, N., Srivastava, M., Kumar, D., & Boneh, D. (2023). Do users write more insecure code with AI assistants? In *Proceedings of the 2023 ACM SIGSAC Conference on Computer and Communications Security* (pp. 2785–2799). [https://doi.org/10.1145/3576915.3623157](https://doi.org/10.1145/3576915.3623157)
- Polanyi, M. (2009). *The tacit dimension*. University of Chicago Press. ISBN 9780226672984。初版 1966 年，Doubleday。文中以（Polanyi, 1966）標示。
- Shumailov, I., Shumaylov, Z., Zhao, Y., Papernot, N., Anderson, R., & Gal, Y. (2024). AI models collapse when trained on recursively generated data. *Nature, 631*(8022), 755–759. [https://doi.org/10.1038/s41586-024-07566-y](https://doi.org/10.1038/s41586-024-07566-y)。2025 年有作者更正（*Nature, 640*, E6, [https://doi.org/10.1038/s41586-025-08905-3](https://doi.org/10.1038/s41586-025-08905-3)），更正理論設定中的參數記號。
- Spracklen, J., Wijewickrama, R., Sakib, A. H. M. N., Maiti, A., Viswanath, B., & Jadliwala, M. (2025). We have a package for you! A comprehensive analysis of package hallucinations by code generating LLMs. In *34th USENIX Security Symposium* (pp. 3687–3706). [https://arxiv.org/abs/2406.10279](https://arxiv.org/abs/2406.10279)
- Willison, S. (2025, May 6). *A quote from Daniel Stenberg*. [作者網頁](https://simonwillison.net/2025/May/6/daniel-stenberg/)。Stenberg 的原始 LinkedIn 貼文需登入才能閱讀，文中引句依此轉引。