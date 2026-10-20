+++
title = "當系統的輸出被當成證據：舉證責任、被隔離的反證與分數的假精確"
date = "2026-10-03T06:20:03+08:00"
author = "么么陸"
draft = false
isCJKLanguage = true
description = "設想一位郵局分局長某天結帳，終端機顯示帳上短少 3,000 英鎊。她確定自己沒有拿錢，也找不到錯在哪裡。她能怎麼證明？她看不到系統的原始碼，看不到資料庫的交易紀錄，也不知道遠在另一個城市的工程師能不能從後台改動她的帳。她手上唯一的證據是自己的證詞，而對方手上的證據是一張由電腦印出來的帳表。"
tags = [
    "分析論述", # term:AnalyticalEssay
    "機器學習", # term:MachineLearning
    "判斷", # term:Judgment
    "人在迴路", # term:HumanInTheLoop
    "似然比", # term:LikelihoodRatio
    "觀察", # term:Observation
    "古哈特定律", # term:GoodhartSLaw
    "自動化偏誤", # term:AutomationBias
  ]
series = ["拆掉自己的煞車：組織如何在合理、合法甚至高效的名義下，失去發現自身錯誤的能力"]
term_exclude = ["Claim", "Identity", "Intent", "Quantization"]
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

設想一位郵局分局長某天結帳，終端機顯示帳上短少 3,000 英鎊。她確定自己沒有拿錢，也找不到錯在哪裡。她能怎麼證明？她看不到系統的原始碼，看不到資料庫的交易紀錄，也不知道遠在另一個城市的工程師能不能從後台改動她的帳。她手上唯一的證據是自己的證詞，而對方手上的證據是一張由電腦印出來的帳表。

本文要回答的問題是：當一個系統的輸出被用來對個人做出不利的決定時，證明「系統錯了」的責任落在誰身上？一個機構如何在沒有任何人說出明顯謊言的情況下，讓第一線的反證失去作用？

這個問題影響三種人。被系統判定有問題的人，需要知道哪些證據在結構上對他不可得；設計與營運這類系統的工程師，需要知道系統的哪些紀錄會成為別人的救命證據；在裁決流程中「最後簽名」的審查者與法官，需要知道一個看起來精確的分數或帳表能證明什麼、不能證明什麼。讀完本文，讀者應能檢查一個以系統輸出為依據的處分流程：系統出錯的可能性在推論中被設為多少？能證明系統出錯的資料由誰持有？一個人遇到的問題，是否被和其他人遇到的同類問題放在一起看？

本文的答案分四步。第一步，說明「推定電腦正常運作」這條證據規則如何把舉證成本放到最缺乏資料的一方。第二步，用機率推理說明一筆短缺能證明什麼，並指出一種有效卻不顯眼的反證壓制：不讓個人知道還有多少人遇到同樣的問題。第三步，處理風險分數，說明為什麼小數點後的精確不等於**判斷**（Judgment） <!-- term:Judgment -->的準確，以及分數如何製造出證實自己的資料。第四步，討論**人在迴路**（Human-In-The-Loop） <!-- term:HumanInTheLoop -->何時只是在形式上存在，以及現行法律提供了什麼。

> [!IMPORTANT]
> **判斷** <!-- term:Judgment --> (Judgment): 面對規則未覆蓋或情境已改變時，根據脈絡評估決定的實質後果。 <!-- anchor:Judgment -->
> **人在迴路** <!-- term:HumanInTheLoop --> (Human-In-The-Loop): 人類節點對自動化決策保留實質否決與阻尼的控制條件，而非形式性的一鍵通過。 <!-- anchor:HumanInTheLoop -->


證據以兩起經官方調查的事件為主：英國郵局 Horizon 系統案與荷蘭托兒津貼案。兩起事件的事實取自法院判決、國會與公共調查報告、主管機關的處分與統計機關的分析；兩案的性質不同，前者主要是帳務系統的錯誤被當成犯罪證據，後者主要是風險評估與行政程序把小錯誤升級為詐欺處分。本文的機率模型是設定，用來檢查推理，不是對兩案的重建。

## 分析

### 一、推定與舉證：誰要證明系統錯了

在英格蘭與威爾斯，1984 年的《警察與刑事證據法》第 69 條曾規定：檢方若要以電腦產生的紀錄作為證據，須先證明該電腦在相關時間正常運作，或其不正常運作並未影響紀錄的產生與內容的準確。英格蘭及威爾斯法律委員會在 1997 年的報告中建議廢除這項要求，國會隨後以 1999 年《少年司法與刑事證據法》第 60 條使第 69 條失效（Police and Criminal Evidence Act 1984; Youth Justice and Criminal Evidence Act 1999）。此後法院回到普通法的推定：在沒有相反證據時，推定機器運作正常。這是一個可以被推翻的推定，但推翻它需要證據（Ministry of Justice, 2025）。

這條規則的理由並非沒有道理。要求每一份電腦紀錄都附上完整的可靠性證明，會讓大量日常案件難以進行，而絕大多數時候電腦確實正常。問題出在推定與資料持有的不對稱結合在一起。能證明系統出錯的東西，例如原始碼、交易日誌、已知錯誤清單、遠端存取紀錄，全部掌握在系統的擁有者與供應商手上。起訴方本有揭露可能有利於被告之資料的義務，但當起訴方本身就是系統擁有者時，揭露是否完整，取決於最有理由不揭露的一方。Horizon 案正是如此：英國刑事案件複審委員會把郵局未揭露系統可靠性問題列為這些定罪的缺失之一（Criminal Cases Review Commission, n.d.）。被告要推翻推定，需要的正是對方持有、而對方未必完整交出的資料。

軟體工程師 James Christie（2020）在〈郵局 Horizon 醜聞與電腦證據可靠性推定〉中，把這項推定形容為「天真且站不住腳」：複雜軟體幾乎必然含有錯誤，而錯誤是否影響某一筆特定交易，正是推定所跳過的問題。一群法律與軟體可靠性學者隨後提出建議，主張以兩階段揭露取代全有或全無的推定：先要求系統擁有者揭露已知錯誤紀錄與相關的控制措施，再依情況要求更深入的資料（Marshall et al., 2021）。英國司法部在 2025 年 1 月就是否改革這項推定公開徵求意見，討論的範圍限於「由軟體產生」的證據，包括人工智慧與演算法的輸出，而不是單純被電腦記錄下來的資料（Ministry of Justice, 2025）。

美國法學者 Danielle Citron（2008）在〈技術性正當程序〉中，從行政程序的角度描述了同一個問題：自動化系統做出不利決定後，聽證官往往傾向「推定電腦系統不會出錯」，讓當事人的申辯形同虛設。換句話說，即使沒有正式的法律推定，裁決者心中的推定也會產生同樣的效果。

### 二、一筆短缺能證明什麼：基準率與被藏起來的證據

一筆帳上的短缺，是支持「分局長拿了錢」的證據嗎？要回答這個問題，需要比較兩種解釋：短缺來自偷竊，或短缺來自系統錯誤。機率推理的標準形式是：

$$
\frac{P(\text{偷竊} \mid \text{短缺})}{P(\text{錯誤} \mid \text{短缺})} = \frac{P(\text{偷竊})}{P(\text{錯誤})} \times \frac{P(\text{短缺} \mid \text{偷竊})}{P(\text{短缺} \mid \text{錯誤})}
$$

等號右邊第一項是事前的比值，第二項是**似然比**（Likelihood Ratio） <!-- term:LikelihoodRatio -->：在兩種解釋下，**觀察**（Observation） <!-- term:Observation -->到短缺的機率之比。如果偷竊與系統錯誤都一定造成短缺，似然比 <!-- term:LikelihoodRatio -->接近 1，短缺本身幾乎不能區分兩種解釋。結論於是完全取決於事前的比值，也就是你一開始認為系統出錯的機率有多高。

> [!IMPORTANT]
> **似然比** <!-- term:LikelihoodRatio --> (Likelihood Ratio): 在特定假設成立與不成立下觀測到同一徵候的條件機率之比，決定貝氏後驗更新的幅度。 <!-- anchor:LikelihoodRatio -->
> **觀察** <!-- term:Observation --> (Observation): 規格工作流中對指定環境、介面或系統版本現況進行檢查所獲得的事實紀錄。 <!-- anchor:Observation -->


法律上的電腦可靠性推定可以被推翻，它不等於把機率設為零：一旦有證據顯示電腦可能沒有正常運作，依賴紀錄的一方就要證明它正常（Ministry of Justice, 2025）。但推翻需要證據，而證據在對方手上。當反證的門檻高到當事人實際上拿不出來時，推定在裁決中的效果，就接近把 $P(\text{錯誤})$ 設為 0。本文把這個極端稱為僵化的推定，它是本文設定，用來說明推理的結構，不是法律規則的形式含義。在僵化的推定之下，任何短缺都只剩一種解釋，而這不是推理的結論，是推理開始前就做好的假設。

#### 其他人的短缺是重要的診斷證據

一筆短缺不能區分兩種解釋，但許多筆短缺可以。設想一個有 1,000 間分局的網路。若沒有系統錯誤，各分局發生短缺的原因是各自獨立的偷竊，發生率很低；若系統有錯誤，錯誤會在許多分局同時造成無法解釋的短缺。所以，「還有多少其他分局也出現同類短缺」這個數字，對判斷 <!-- term:Judgment -->是不是系統錯誤很有診斷力。

本文設定以下模型。系統存在錯誤的事前機率為 $\pi$。每間分局發生偷竊的機率為 $t$；若系統有錯誤，每間分局另外以機率 $q$ 出現錯誤造成的短缺，與偷竊彼此獨立。現在已知某一間分局有短缺，另外 $M$ 間分局中有 $k$ 間也有短缺。問題是：這一間分局確實發生偷竊的機率是多少？（在有錯誤的世界裡，偷竊與錯誤可能同時發生，所以這個機率問的是「有沒有偷竊」，不是「短缺全部由偷竊造成」。）

```python
# 本文設定：所有數值皆為設想，用來檢查推理，不重建任何真實的錯誤率或犯罪率
from math import lgamma, log, exp

def log_binom(n, k, p):
    return lgamma(n + 1) - lgamma(k + 1) - lgamma(n - k + 1) + k * log(p) + (n - k) * log(1 - p)

def logistic_from_log_odds(x):
    return 1 / (1 + exp(-x)) if x >= 0 else exp(x) / (1 + exp(x))

def p_theft_given_data(prior_bug, theft, bug_rate, others, others_short):
    if not (0 <= prior_bug <= 1 and 0 < theft < 1 and 0 < bug_rate < 1):
        raise ValueError("invalid probability")
    if not (isinstance(others, int) and isinstance(others_short, int) and 0 <= others_short <= others):
        raise ValueError("invalid counts")
    short_if_bug = 1 - (1 - theft) * (1 - bug_rate)
    if prior_bug == 0:
        post_bug = 0.0
    elif prior_bug == 1:
        post_bug = 1.0
    else:
        # 兩種世界下，觀察到「本分局短缺」且「其他 others 間中有 others_short 間短缺」的對數似然
        ll_bug = log(short_if_bug) + log_binom(others, others_short, short_if_bug)
        ll_none = log(theft) + log_binom(others, others_short, theft)
        post_bug = logistic_from_log_odds(log(prior_bug) + ll_bug - log(1 - prior_bug) - ll_none)
    # 無錯誤的世界：短缺必來自偷竊；有錯誤的世界：在已知短缺下，有偷竊的機率為 t / P(短缺)
    return (1 - post_bug) + post_bug * theft / short_if_bug

M, t, q, prior = 999, 0.002, 0.05, 0.1

isolated = p_theft_given_data(prior, t, q, M, 2)     # 其他分局的短缺數與單純偷竊相符
widespread = p_theft_given_data(prior, t, q, M, 50)  # 其他分局普遍出現短缺
rigid = p_theft_given_data(0.0, t, q, M, 50)         # 僵化的推定：系統不會錯

assert isolated > 0.9
assert widespread < 0.1
assert rigid == 1.0      # 在僵化的推定之下，其他人的遭遇完全不影響結論
assert 0 < p_theft_given_data(prior, t, q, 1_000_000, 0) <= 1   # 極端輸入不溢位
```

依所列設定，若系統沒有錯誤，其他 999 間分局中平均約有 2 間短缺；若系統有錯誤，平均約有 52 間。當觀察 <!-- term:Observation -->到的其他短缺數是 2 時，這間分局確實有偷竊的機率很高；當觀察 <!-- term:Observation -->到的是 50 時，機率降到很低。而在僵化的推定之下，不論其他分局有多少短缺，結論都是偷竊。讀者可以修改 $\pi$、$t$、$q$ 驗證這個結論的方向不依賴特定數值：只要系統錯誤會同時影響許多分局，$k$ 就具有很強的診斷力。

這個模型的限制要說清楚。它假設各分局獨立、錯誤率對各分局相同、分局長不會為了脫罪而虛報，也忽略了誠實的人為帳務錯誤等其他原因；加入這些原因，會進一步降低「偷竊」的後驗機率，但不改變 $k$ 的作用方向。它能檢查的只有一條規則：$k$ 對判斷 <!-- term:Judgment -->的影響，以及僵化的推定如何讓這個影響歸零。在真實案件中，交易日誌、已知錯誤紀錄等直接證據同樣關鍵，$k$ 是其中一項，而且是個別當事人最難自己取得的一項。

#### 個案：Horizon 系統

英國郵局的分局使用富士通開發與維護的 Horizon 帳務系統。2019 年，高等法院法官 Peter Fraser 在一場由分局長提起的集體訴訟中，就系統的技術問題作出判決。判決認定，Horizon 的錯誤與缺陷能夠造成分局帳上的短缺，而且「確實發生過，而且發生過許多次」；判決也認定，富士通的員工能夠遠端存取分局的資料，並且能在分局長不知情或未同意的情況下修改資料、實施修補與重建資料（Bates v Post Office Ltd (No 6), 2019）。

判決也讓「系統錯誤會造成短缺」從抽象變成具體。郵局本身承認的錯誤之一是舊版 Horizon 的「Callendar Square」錯誤，原告方專家描述它會讓系統無法辨認庫存單位之間的轉移；法官認定它至少直接影響了 19 間分局的帳，而富士通並未查證受影響分局的總數；記錄受影響分局的試算表，直到開庭前七個工作天才交給原告（§§425–426）。新版系統 Horizon Online 另有一個「收付不符」錯誤，會造成同樣的效果（§427）。最與本節模型相關的是郵局對通知的立場：郵局主張，把 Callendar Square 錯誤，以及另一個後來被承認的 Dalmellington 錯誤的存在，告知所有分局長或受影響的分局長「沒有必要，也不適當」，並說明通知有「實際的缺點」（§§437–439）。用前面模型的語言說，這就是把 $k$ 留在系統擁有者手上。

在判決之前，郵局以私人起訴權起訴了 700 多名分局長（Criminal Cases Review Commission, n.d.）。英國上議院圖書館的整理顯示，郵局起訴的案件約有 700 件定罪，加上其他檢察機關起訴的 283 件，共約 983 件（Coleman, 2025）。2024 年 5 月 24 日，《郵局（Horizon 系統）罪行法》獲得御准，撤銷符合條件的定罪（Department for Business and Trade, 2024）。

這段歷史中，有幾次「其他人也有問題」的證據出現，又沒有被用上。2013 年 7 月，郵局委託的獨立會計師事務所 Second Sight 在期中報告中指出，有兩起系統缺陷事件造成 76 間分局的餘額或交易錯誤；報告同時表示，截至當時尚未發現全系統性問題的證據（Henderson & Warmington, 2013）。同一個月，郵局的外部律師 Simon Clarke 在法律意見中指出，富士通的技術證人在先前的審判中沒有揭露他所知道的系統缺陷，此一失職「明顯違反他作為專家證人的義務」，其可信度已「致命地受損」；2013 年 8 月的另一份意見則警告，若刻意避免記錄與保留可能需要揭露的資料，可能構成妨害司法的共謀（Clarke, 2013a, 2013b）。這兩份意見是 Clarke 本人的評估，不是法院的認定。之後，郵局在 2015 年單方面終止了調解計畫與 Second Sight 的委任（Warmington & Henderson, 2020）。

用第二節的模型看，Second Sight 的報告與 Clarke 的意見都在增加「不只你一個人」這類證據的可見度。依本文推論，它們沒有改變多數案件的結果，重要原因之一是證據沒有被送到需要它的被告與法庭手上；公共調查與法院也追究了個別機構與人員的責任，兩者並不互斥。許多分局長後來陳述自己曾被告知「只有你遇到這個問題」；這個說法廣泛見於報導，但本文所引的官方文件沒有記載，因此只把它當作模型所描述機制的一個可能形式，而不當作已證實的事實。

公共調查的第一卷報告在 2025 年 7 月發布，記錄了監禁、財務損失、心理傷害與家庭破裂等後果。報告也記載，有 13 人的死亡被家屬歸因於 Horizon 事件，但主持調查的退休法官 Wyn Williams 表示，他無法對所有 13 人的死亡與 Horizon 之間的因果關係作出確定的認定（Williams, 2025）。

### 三、分數的假精確與自我證實

Horizon 的輸出是一張帳表，荷蘭托兒津貼案的輸出則是一個風險評分。分數帶來的問題有一部分與帳表相同，另有一部分是分數特有的。

荷蘭稅務機關使用一套風險分類模型，從大量托兒津貼申請中挑出需要進一步審查的案件。荷蘭個資保護主管機關在 2020 年的調查中發現，該模型把申請人「是否具有荷蘭國籍」作為指標之一；2021 年 12 月，主管機關就稅務機關以違法且具歧視性的方式處理國籍資料，處以 275 萬歐元罰款，並在 2022 年 4 月就另一份名為 FSV 的詐欺嫌疑名單另罰 370 萬歐元（Autoriteit Persoonsgegevens, 2020, 2021, 2022）。國會調查報告與國際特赦組織都指出，這個模型含有自我學習的成分（Parlementaire ondervragingscommissie Kinderopvangtoeslag, 2020; Amnesty International, 2021）。

被挑出的家庭面對的是一種「全有或全無」的處理：只要申請文件有缺漏或小錯，稅務機關就要求退還全部已領津貼，而不是只退還有問題的部分。國會調查委員會在 2020 年 12 月的報告《前所未有的不公》中認定，這種做法並非法律條文本身的要求，而是行政執行與司法解釋共同造成的結果，並且認定「法治的基本原則遭到違反」（Parlementaire ondervragingscommissie Kinderopvangtoeslag, 2020）。2021 年 1 月 15 日，呂特第三屆內閣請辭（Ministerie van Algemene Zaken, 2021）。荷蘭最高行政法院的行政審判庭在 2021 年 11 月的反省報告中承認，它對這類案件的審查方向轉得太晚，「這個轉向本可以、也本應該更早發生」（Raad van State, 2021）。

受影響的規模至今仍在核定中。截至 2024 年 1 月，登記申請補救的人數約 68,000 人；政府在 2024 年 9 月表示，已認定的受害者超過 37,000 人（NOS, 2024; Ministerie van Financiën, 2024）。常被引用的「上千名兒童被帶離家庭」需要特別小心。荷蘭中央統計局統計到，2015 年至 2022 年 6 月間，受影響家長的子女中有 2,090 人曾被安置於家庭以外；但統計局明確表示，這「並不表示這些安置是受害造成的」，與條件相近的家庭比較，也沒有發現受害後兒童保護措施增加的跡象（Centraal Bureau voor de Statistiek, 2021, 2022）。這不排除個別家庭的安置與此案有關，但不能用這個數字證明因果。

#### 精確不等於準確

一個顯示為 0.892 的風險分數（設想），看起來比「高風險」三個字精確得多。但分數的小數位數只代表解析度：它能區分多細的差異。它不代表準確度：分數為 0.9 的案件中，是否真的有九成有問題。後者稱為**校準**（Calibration） <!-- term:Calibration -->，必須用事後的結果去檢查，不能從分數的外觀看出來。

> [!IMPORTANT]
> **校準** <!-- term:Calibration --> (Calibration): 模型輸出機率與實際正確率的一致程度。 <!-- anchor:Calibration -->


分數還有一個更根本的限制：它把許多不同的情形壓成同一個數字。設想兩份申請：一位家長因為重病而遲交了收入證明的附件，另一份則來自有組織的詐領。如果模型使用的是「文件遲交天數」「是否曾補正申報」這類容易取得的代理特徵，兩份申請可能得到幾乎相同的分數。分數一樣，不表示案情一樣；區分兩者需要的是分數沒有記錄的資訊，而那正是當事人的陳述與個案調查能提供的。

即使模型的敏感度高、誤報率低，基準率仍可能讓多數被標記的案件是誤判。本文設定：申請案件中真正詐欺的比例為 $p$；模型能抓出其中比例為 $s$ 的案件（敏感度）；在非詐欺案件中，模型以比例 $f$ 誤標為高風險；三者都在 0 與 1 之間，而且至少有一些案件被標記。被標記的案件中真正詐欺的比例是：

$$
\text{PPV} = \frac{p \, s}{p \, s + (1 - p) \, f}
$$

滿足直覺的例子（設想）：$p = 0.2$、$s = 0.9$、$f = 0.05$，則 $\text{PPV} = 0.18 / (0.18 + 0.04) \approx 0.82$，被標記者八成以上確實有問題。違反直覺的例子（設想）：同樣的模型，但真正詐欺的比例只有 $p = 0.01$，則 $\text{PPV} = 0.009 / (0.009 + 0.0495) \approx 0.15$，被標記者中八成五並沒有詐欺。模型完全相同，差別只在於它被用在一個多數人誠實的群體上。這也說明校準 <!-- term:Calibration -->與敏感度、誤報率是不同的性質：同一個門檻搬到基準率不同的群體，敏感度與誤報率不變，分數的校準 <!-- term:Calibration -->卻不再成立。這時若對每個被標記者都採取全有或全無的處分，處分的主要對象將是誠實的人。

#### 分數如何製造證實自己的資料

分數還有一個帳表沒有的問題：它決定了哪些資料會被產生。被標記為高風險的家庭受到逐張單據的審查，小錯誤因而被發現並記錄為違規；未被標記的家庭沒有受到同等審查，同樣的小錯誤不會被記錄。若這些「查到的違規」再被當作模型的訓練資料或成效指標，模型就會在它原本懷疑的群體身上找到更多證據，並因此更懷疑這個群體。

Ensign 等人（2018）在預測性警務的研究中，以數學模型證明了這種失控的**回饋迴路**（Feedback Loop） <!-- term:FeedbackLoop -->：當被觀察 <!-- term:Observation -->到的犯罪取決於警力被派到哪裡，演算法會把警力「一再送回同樣的街區，而與真實的犯罪率無關」。這個結論來自模型，不代表每一套部署中的系統都會如此，但它指出了一個需要被檢查的結構：系統用來評估自己的資料，是否由系統自己的決定所產生。

> [!IMPORTANT]
> **回饋迴路** <!-- term:FeedbackLoop --> (Feedback Loop): 用於持續觀測治理機制運營效能的閉環系統，通常包含規則遵守率、規則有效性與治理摩擦成本三個觀測層次，藉以驅動治理規則的動態調整。 <!-- anchor:FeedbackLoop -->


這與經濟學與社會科學中兩條常被引用的規律相連。英國經濟學家 Goodhart 在 1975 年寫道：「任何被觀察 <!-- term:Observation -->到的統計規律，一旦被施加控制的壓力，就會趨於瓦解。」（McIntyre, 2001 轉引）今天流傳的版本「當一個量測變成目標，它就不再是好的量測」，其實出自人類學家 Marilyn Strathern（1997）對英國大學評鑑的重述。社會心理學家 Donald Campbell（1979）的說法更直接：一個量化社會指標越被用於社會決策，就越受到腐化的壓力，也越容易扭曲它本來要監測的社會過程。本文把這兩條規律稱為**古哈特定律**（Goodhart's Law） <!-- term:GoodhartSLaw -->與**坎貝爾定律**（Campbell's Law） <!-- term:CampbellSLaw -->，並把風險分數看成它們的一個具體情形：當「查獲的違規數」成為**代理指標**（Proxy Metric） <!-- term:ProxyMetric -->，審查就會往容易查獲的地方集中。

> [!IMPORTANT]
> **古哈特定律** <!-- term:GoodhartSLaw --> (Goodhart's Law): 當一項度量變成目標時，它便不再是好的度量；官僚演算法把代理變數當成犯罪傾向本身。 <!-- anchor:GoodhartSLaw -->
> **坎貝爾定律** <!-- term:CampbellSLaw --> (Campbell's Law): 定量社會指標一旦用於決策，就越容易被腐化並扭曲其原本要監控的社會進程。 <!-- anchor:CampbellSLaw -->
> **代理指標** <!-- term:ProxyMetric --> (Proxy Metric): 用可觀察的量或摘要代替無法直接觀察的真實狀態，可能在被用於控制後與狀態脫鉤。 <!-- anchor:ProxyMetric -->


### 四、人在迴路何時只是在場

面對自動化決策的批評，最常見的回應是：最終決定仍由人做出。但人在迴路 <!-- term:HumanInTheLoop -->有沒有用，取決於那個人能做什麼，而不是他在不在。

心理學研究記錄了**自動化偏誤**（Automation Bias） <!-- term:AutomationBias -->：人在有自動化輔助時，傾向遺漏輔助沒有提示的問題，也傾向跟從輔助的錯誤建議（Skitka, Mosier, & Burdick, 1999）。科技人類學家 Madeleine Elish（2019）則描述了一種責任分配的結構，稱為道德潰縮區：在人與自動系統共同運作的系統中，一旦出事，責任常被歸到那個對系統只有有限控制權的人身上，就像汽車的潰縮區吸收撞擊、保護了車內的其他部分一樣。法學者 Ben Green（2022）檢視了 41 項要求人類監督政府演算法的政策，認為這些政策常給人「虛假的安全感」，並讓供應商與機關得以逃避責任；他主張以有實證基礎的制度性監督取代個人層次的監督要求。

> [!IMPORTANT]
> **自動化偏誤** <!-- term:AutomationBias --> (Automation Bias): 使用者因倚賴自動化建議而降低獨立查核，未能辨識系統新引入錯誤的傾向。 <!-- anchor:AutomationBias -->


這些研究合起來，支持本文的一個綜合推論：一個人工審查步驟要有實質作用，至少需要三個條件，缺一不可：

1. **資訊**：審查者能看到系統沒有使用或沒有呈現的資訊，例如當事人的陳述、其他類似案件的情況，或系統已知的錯誤紀錄。
2. **權限與時間**：審查者有權推翻系統的結論，並有足夠時間做出獨立判斷 <!-- term:Judgment -->。
3. **對稱的問責**：推翻系統與跟從系統，在事後被追究的方式相同。若推翻系統需要寫長篇說明、事後出錯要負責，而跟從系統出錯可以說「我依系統辦理」，理性的審查者會選擇跟從。

依本文引用的資料，兩案在第一個條件上都有缺口，但缺口不同。Horizon 案中，系統缺陷與遠端修改能力的資訊，沒有完整送到被告與法庭手上；托兒津貼案中，風險模型使用國籍指標、全有或全無的做法並非法律要求，這些事實在多年後才由調查揭露。這是資訊在結構上沒有被送到審查的位置；它與個別審查者、起訴方或法院未盡職責可以並存，荷蘭國會調查報告就明確批評了行政與司法機關。

法律開始對其中一部分作出回應。歐盟《一般資料保護規則》第 22 條保護個人免受「完全基於自動化處理」且產生法律效果的決定。歐洲法院在 2023 年 12 月的 SCHUFA 案中認定，當第三方在決定是否與某人訂約時「高度倚賴」信用評分機構算出的機率值，該評分本身就可能構成第 22 條所說的決定，即使形式上做決定的是另一個機構（OQ v Land Hessen, 2023）。歐盟《人工智慧法》第 14 條要求高風險人工智慧系統的人類監督者能意識到「自動依賴或過度依賴」系統輸出的傾向，第 86 條則在特定條件下賦予受影響者取得決策說明的權利（European Parliament & Council, 2024）。這些條文的適用範圍都有限制，例如第 86 條只適用於特定高風險系統且有例外，它們不能直接回答第二節提出的問題：誰負責把「其他人也遇到同樣問題」的證據，送到正在審查個案的人手上。

#### 證言為什麼不被相信

哲學家 Miranda Fricker（2007）在《**認識論不公**（Epistemic Injustice） <!-- term:EpistemicInjustice -->》中區分了兩種不公。**證言不公**（Testimonial Injustice） <!-- term:TestimonialInjustice -->是指聽者因偏見，給說話者的證言打了不應有的折扣；詮釋不公則是指社會缺乏可用的概念，讓某些人無法理解或說明自己的經歷。認識論不公 <!-- term:EpistemicInjustice -->是兩者的總稱。

> [!IMPORTANT]
> **認識論不公** <!-- term:EpistemicInjustice --> (Epistemic Injustice): 因制度位置而使主體證言被系統性降級、喪失被聽見資格的認識論傷害。 <!-- anchor:EpistemicInjustice -->
> **證言不公** <!-- term:TestimonialInjustice --> (Testimonial Injustice): 說話者因制度偏見而被系統性降低信用權重的認識論傷害。 <!-- anchor:TestimonialInjustice -->


用這組概念看兩起事件，要注意它們與 Fricker 原意的距離。Fricker 的典型情形是針對身分的偏見。托兒津貼案較接近這個情形：國籍成為風險指標，使某些群體的申請與申辯在一開始就被打折扣。Horizon 案則有所不同：分局長的證言被打折扣，主要不是因為他們的身分，而是因為與之對立的證據來自一台被推定正確的機器。本文把後者理解為一種結構性的可信度落差：機器輸出獲得了超額的可信度，人的證言相對被壓低。這是本文詮釋，是對 Fricker 概念的延伸使用。詮釋不公的概念也有幫助：一位分局長若沒有「並行交易衝突」或「遠端資料修補」這類詞彙，就很難把「帳不對但我沒拿錢」說成一個能被法庭理解的技術主張。

組織研究稱這種狀態為**組織沉默**（Organizational Silence） <!-- term:OrganizationalSilence -->：組織成員普遍保留對問題的資訊與意見，因為說了沒用，甚至會受罰（Morrison & Milliken, 2000）。兩起事件中的沉默，不一定是有人下令噤聲，更常見的是每一個提出異議的人都被當作孤立的個案處理，異議因此無法匯集成可以推翻推定的證據。這是本文詮釋。反過來看，Horizon 案最後由一群分局長共同提起的集體訴訟揭露，說明集體行動能把分散的個案變成可以被法庭檢驗的模式。依本文建議，匯集異議的窗口要發揮作用，提出異議的人必須不因此在考績、薪酬或合約上受罰，而工會、分局長協會這類集體代表，能降低個人提出異議的成本。

> [!IMPORTANT]
> **組織沉默** <!-- term:OrganizationalSilence --> (Organizational Silence): 第一線知情無法轉化為系統修正時，由制度性恐懼所維繫的發聲停滯。 <!-- anchor:OrganizationalSilence -->


## 反思

**推定本身不是錯。** 法律推定電腦正常運作，是因為大多數時候它確實正常，而要求每一份紀錄都證明可靠性的成本極高。本文的論證不支持完全倒置舉證責任，那會讓大量正常的案件無法處理。它支持的是更窄的主張：當處分嚴重、系統複雜、而能推翻推定的資料都在一方手上時，持有資料的一方應負揭露義務。兩階段揭露的建議正是在這兩端之間找位置。

**匯集異議有成本。** 第二節的模型顯示，其他人的遭遇是很有診斷力的證據。但匯集異議需要有人負責收集、分類與判斷 <!-- term:Judgment -->，也可能被濫用：一群人可以協調虛報。模型假設各分局獨立陳述，真實情況下需要獨立的驗證。這不改變結論的方向，但說明匯集機制本身需要設計。

**演算法也可能減少偏見。** 本文的兩個個案都是演算法與行政程序結合後造成傷害，但人的裁量也帶有偏見，結構化的評分在某些情境下能讓決定更一致。問題不在於用不用分數，而在於分數被用來做什麼：用來決定先審查誰，與用來直接決定處分，風險完全不同；把分數當作調查起點，與把分數當作有罪證據，也完全不同。

**公開與稽核有邊界。** 常見的主張是，用於公權力的演算法應完全公開特徵權重與訓練資料，接受常態化的對抗性稽核。完全公開會碰到兩個限制：訓練資料含有個人資料，而公開的規則也可能被針對性地規避。本文建議較窄的做法：讓獨立於使用單位的稽核者取得完整的模型與資料，對外公開稽核的問題與結論。Raji 等人（2020）提出的內部稽核架構，把稽核嵌入演算法開發的每個階段，每個階段都產生文件，合起來成為一份完整的稽核報告；依本文推論，這類文件正是外部稽核者與法院在爭議發生時需要的資料。內部稽核的限制是它由系統擁有者自己執行，所以它補強的是資料的存在，不是判斷 <!-- term:Judgment -->的獨立。

**個案的結局不能倒推每一步的意圖。** Horizon 與托兒津貼案都在多年後被揭露，事後回看，每一個沒有採取行動的時刻都顯得難以理解。本文刻意只描述結構：推定、資料持有、匯集的缺席、全有或全無的處分。結構可以解釋為什麼錯誤持續那麼久，但不能證明每一位參與者都知情。

**法律條文的覆蓋仍有限。** SCHUFA 案與《人工智慧法》處理的是自動化決定與高風險系統，Horizon 那樣的帳務系統不是在做「決定」，它只是記錄；它的輸出成為證據後，才產生決定的效果。這類「記錄型系統」被當作證據時的可靠性問題，正是英國司法部 2025 年徵求意見所處理的。截至該次徵求在 2025 年 4 月截止時，它仍是一個開放的政策問題；其後的立法進度不在本文的引用範圍內。

## 實務對比

同樣以系統輸出為起點，一個機構可以用三種方式處理第一線的反證。下表比較它們在本文三個檢查問題上的差異。第三欄是本文建議，結合了兩階段揭露的學術建議與本文第二節的推理，不是任何機構的現行做法。

| 檢查問題 | 輸出即事實 | 輸出為主張，個案審查 | 輸出為主張，跨案匯集（本文建議） |
| :--- | :--- | :--- | :--- |
| 系統出錯的可能性 | 推定為零 | 可被推翻，但由當事人舉證 | 可被推翻，系統擁有者先揭露已知錯誤紀錄 |
| 證明錯誤的資料 | 擁有者持有，不揭露 | 當事人申請後揭露 | 擁有者主動揭露與案件相關的錯誤與遠端修改紀錄 |
| 系統的獨立稽核 | 無 | 出事後委託，可由委託者終止 | 由不隸屬使用單位的稽核者定期執行，結論公開 |
| 其他人的同類遭遇 | 不考慮 | 不考慮 | 由獨立於處分單位的窗口匯集，並告知審查者同類案件的數量 |
| 處分方式 | 全有或全無 | 依個案比例 | 依個案比例；申辯期間暫停扣薪、查封等不可逆的執行；分數只用於排定審查順序，不作為處分依據 |
| 提出異議者的保護 | 無 | 依一般申訴程序 | 異議不影響考績、薪酬或合約；允許集體代表代為提出 |

兩起事件的走向也可以用同一組問題對照。下表只整理本文引用的事實，不比較兩起事件的嚴重程度。

| 對照項 | Horizon 系統案 | 荷蘭托兒津貼案 |
| :--- | :--- | :--- |
| 系統輸出 | 分局帳表上的短缺 | 申請案件的風險分類 |
| 被推定的事 | 電腦正常運作（證據法） | 被挑出者有詐欺嫌疑（行政實務） |
| 被藏起或未匯集的證據 | 系統缺陷、遠端修改能力、其他分局的短缺 | 模型使用國籍指標、全有或全無做法並非法律要求 |
| 讓錯誤被看見的機制 | 分局長集體訴訟與法院判決、公共調查 | 新聞調查、國會調查委員會、主管機關調查 |
| 制度的事後回應 | 立法撤銷定罪；司法部就推定徵求意見 | 內閣請辭；最高行政法院自我檢討；補救程序 |

兩起事件中，讓錯誤最終被看見的都不是原本的審查程序，而是一個能把許多個案放在一起看的機制：集體訴訟、國會調查或新聞調查。這是本文第二節模型在現實中的對應。

## 結論

當系統的輸出被當成對個人不利的證據時，誰要證明系統錯了，往往在推理開始前就已決定。推定電腦正常運作在法律上可以被推翻，但推翻需要的資料在對方手上；當反證實際上拿不出來，推定就僵化成把系統出錯的機率設為零，任何短缺、任何高分都只剩一種解釋。推定本身有其理由，但它與資料持有的不對稱結合後，就把舉證成本放到了最沒有資料的一方。

一個機構不需要說謊，也能讓反證失效。一個有效的方式，是讓每一個提出異議的人都被當作孤立的個案。從機率推理看，「還有多少人遇到同樣的問題」是區分個人過失與系統錯誤的重要證據，而且是個別當事人最難自己取得的一項；不匯集、不告知這個數字，就等於把這項證據從推理中拿掉。

風險分數帶來兩個額外的問題。小數點後的精確只是解析度，不是準確度；在多數人誠實的群體中，即使是好模型，被標記者中也可能以誠實者居多。分數還會決定哪些資料被產生，使模型在它懷疑的地方找到更多證據。

讓人在迴路 <!-- term:HumanInTheLoop -->裡有實質作用，需要資訊、權限與時間、對稱的問責三者同時存在。可帶走的檢查只有三個問題：系統出錯的可能性在推論中被設為多少？證明系統出錯的資料由誰持有、誰有義務揭露？一個人的遭遇，有沒有和其他人的同類遭遇放在一起看？

## 參考文獻 (References)

文中以（作者, 年份）標示出處，條目依作者字母排序。讀取日期為 2026-10-03。本文的機率模型、表中數值與「本文建議」欄為設定或建議，不是實測。判決與法規的引述以所列官方文本為準；荷蘭文文件的中文為本文轉譯。

- Amnesty International. (2021, October). *Xenophobic machines: Discrimination through unregulated use of algorithms in the Dutch childcare benefits scandal* (EUR 35/4686/2021). [官方 PDF](https://www.amnesty.nl/content/uploads/2021/10/20211014_FINAL_Xenophobic-Machines.pdf)。引用範圍：模型含自我學習成分（p. 5）。
- Autoriteit Persoonsgegevens. (2020, July 17). *Werkwijze Belastingdienst in strijd met de wet en discriminerend*. [官方網頁](https://www.autoriteitpersoonsgegevens.nl/actueel/werkwijze-belastingdienst-in-strijd-met-de-wet-en-discriminerend)
- Autoriteit Persoonsgegevens. (2021, December 7). *Boete Belastingdienst voor discriminerende en onrechtmatige werkwijze*. [官方網頁](https://www.autoriteitpersoonsgegevens.nl/actueel/boete-belastingdienst-voor-discriminerende-en-onrechtmatige-werkwijze)
- Autoriteit Persoonsgegevens. (2022, April 12). *Boete Belastingdienst voor zwarte lijst FSV*. [官方網頁](https://www.autoriteitpersoonsgegevens.nl/actueel/boete-belastingdienst-voor-zwarte-lijst-fsv)
- Bates v Post Office Ltd (No 6: Horizon Issues) [2019] EWHC 3408 (QB). [官方判決 PDF](https://www.judiciary.uk/wp-content/uploads/2019/12/bates-v-post-office-judgment.pdf)。引用範圍：§§425–427, 437–439, 549, 968–970, 1002–1003。
- Campbell, D. T. (1979). Assessing the impact of planned social change. *Evaluation and Program Planning, 2*(1), 67–90. [https://doi.org/10.1016/0149-7189(79)90048-X](https://doi.org/10.1016/0149-7189(79)90048-X)
- Centraal Bureau voor de Statistiek. (2021, October 18). *Uithuisplaatsingen onder gedupeerden toeslagenaffaire*. [官方網頁](https://www.cbs.nl/nl-nl/maatwerk/2021/42/uithuisplaatsingen-onder-gedupeerden-toeslagenaffaire)
- Centraal Bureau voor de Statistiek. (2022, November 30). *Actualisatie uithuisplaatsingen toeslagenaffaire 2015 t/m juni 2022*. [官方網頁](https://www.cbs.nl/nl-nl/maatwerk/2022/48/actualisatie-uithuisplaatsingen-toeslagenaffaire-2015-t-m-juni-2022)。2023 年 4 月 26 日更正版。
- Christie, J. (2020). The Post Office Horizon IT scandal and the presumption of the dependability of computer evidence. *Digital Evidence and Electronic Signature Law Review, 17*, 49–70. [https://doi.org/10.14296/deeslr.v17i0.5226](https://doi.org/10.14296/deeslr.v17i0.5226)
- Citron, D. K. (2008). Technological due process. *Washington University Law Review, 85*(6), 1249–1313. [期刊網頁](https://journals.library.wustl.edu/lawreview/article/id/6697/)
- Clarke, S. (2013a, July 15). *Advice on the use of expert evidence relating to the integrity of the Fujitsu Services Ltd Horizon System* (Inquiry exhibit POL00113694). Post Office Horizon IT Inquiry. [官方網頁](https://www.postofficehorizoninquiry.org.uk/evidence/pol00113694-advice-mr-simon-clarke-barristercartwright-king-use-expert-evidence-relating)。文中簡稱 Clarke, 2013a。
- Clarke, S. (2013b, August 2). *Disclosure: The duty to record and retain material* (Inquiry exhibit POL00129453). Post Office Horizon IT Inquiry. [官方網頁](https://www.postofficehorizoninquiry.org.uk/evidence/pol00129453-simon-clarkes-advice-re-disclosure-duty-record-and-retain-material-post-office)。文中簡稱 Clarke, 2013b。
- Coleman, C. (2025, February 20). *Post Office Horizon IT scandal: Progress of compensation*. House of Lords Library. [官方網頁](https://lordslibrary.parliament.uk/post-office-horizon-it-scandal-progress-of-compensation/)
- Criminal Cases Review Commission. (n.d.). *Post Office 'Horizon' cases*. [官方網頁](https://ccrc.gov.uk/post-office-horizon-cases/)
- Department for Business and Trade. (2024, May 24). *Post Office (Horizon) convictions quashed*. [官方網頁](https://www.gov.uk/government/news/post-office-horizon-convictions-quashed)
- Elish, M. C. (2019). Moral crumple zones: Cautionary tales in human-robot interaction. *Engaging Science, Technology, and Society, 5*, 40–60. [https://doi.org/10.17351/ests2019.260](https://doi.org/10.17351/ests2019.260)
- Ensign, D., Friedler, S. A., Neville, S., Scheidegger, C., & Venkatasubramanian, S. (2018). Runaway feedback loops in predictive policing. In *Proceedings of the 1st Conference on Fairness, Accountability and Transparency* (PMLR 81, pp. 160–171). [官方網頁](https://proceedings.mlr.press/v81/ensign18a.html)
- European Parliament & Council. (2024). Regulation (EU) 2024/1689 (Artificial Intelligence Act). *Official Journal of the European Union, L, 2024/1689*. [EUR-Lex](https://eur-lex.europa.eu/eli/reg/2024/1689/oj/eng)。引用範圍：第 14 條、第 86 條的原始文本。
- Fricker, M. (2007). *Epistemic injustice: Power and the ethics of knowing*. Oxford University Press. ISBN 9780198237907
- Green, B. (2022). The flaws of policies requiring human oversight of government algorithms. *Computer Law & Security Review, 45*, 105681. [https://doi.org/10.1016/j.clsr.2022.105681](https://doi.org/10.1016/j.clsr.2022.105681)
- Henderson, I. R., & Warmington, R. J. (2013, July 8). *Post Office Limited: Interim report*. Second Sight Support Services Ltd. （無官方線上來源；報告原件副本見 [postofficescandal.uk](https://www.postofficescandal.uk/wp-content/uploads/2024/04/20130708-POL-Interim-Report-Signed.pdf)）。引用範圍：§8.2。
- Marshall, P., Christie, J., Ladkin, P. B., Littlewood, B., Mason, S., Newby, M., Rogers, J., Thimbleby, H., & Thomas, M. (2021). Recommendations for the probity of computer evidence. *Digital Evidence and Electronic Signature Law Review, 18*, 18–26. [https://doi.org/10.14296/deeslr.v18i0.5240](https://doi.org/10.14296/deeslr.v18i0.5240)
- McIntyre, M. E. (2001). *Goodhart's law*. University of Cambridge, DAMTP. [官方網頁](https://www.damtp.cam.ac.uk/user/mem2/papers/LHCE/goodhart.html)。Goodhart 1975 年原文本次未能讀取，文中引句依此轉引。
- Ministerie van Algemene Zaken. (2021, January 15). *Minister-president Rutte biedt ontslag aan van kabinet*. [官方網頁](https://www.rijksoverheid.nl/actueel/nieuws/2021/01/15/minister-president-rutte-biedt-ontslag-aan-van-kabinet)
- Ministerie van Financiën. (2024, September 13). *Kabinet: alle beoordelingen toeslagenouders in 2025 afgerond*. [官方網頁](https://www.rijksoverheid.nl/actueel/nieuws/2024/09/13/kabinet-alle-beoordelingen-toeslagenouders-in-2025-afgerond)
- Ministry of Justice. (2025, January 21). *Use of evidence generated by software in criminal proceedings: Call for evidence*. [官方網頁](https://www.gov.uk/government/calls-for-evidence/use-of-evidence-generated-by-software-in-criminal-proceedings/use-of-evidence-generated-by-software-in-criminal-proceedings-call-for-evidence)
- Morrison, E. W., & Milliken, F. J. (2000). Organizational silence: A barrier to change and development in a pluralistic world. *Academy of Management Review, 25*(4), 706–725. [https://doi.org/10.5465/amr.2000.3707697](https://doi.org/10.5465/amr.2000.3707697)。本次只核對書目；文中為概念轉述。
- NOS. (2024, January 22). *Bijna 70.000 mensen meldden zich voor deadline als 'toeslagenouders'*. [官方網頁](https://nos.nl/artikel/2505816-bijna-70-000-mensen-meldden-zich-voor-deadline-als-toeslagenouders)
- OQ v Land Hessen (SCHUFA Holding), Case C-634/21, ECLI:EU:C:2023:957 (CJEU, 7 December 2023). [EUR-Lex](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:62021CJ0634)
- Parlementaire ondervragingscommissie Kinderopvangtoeslag. (2020). *Ongekend onrecht* (Kamerstuk 35510, nr. 2). Tweede Kamer der Staten-Generaal. [官方網頁](https://zoek.officielebekendmakingen.nl/kst-35510-2.html)
- Police and Criminal Evidence Act 1984, c. 60, s. 69 (as enacted). [legislation.gov.uk](https://www.legislation.gov.uk/ukpga/1984/60/section/69/enacted)
- Raad van State. (2021, November 19). *Lessen uit de kinderopvangtoeslagzaken*. [官方網頁](https://www.raadvanstate.nl/lessen-uit-de-kinderopvangtoeslagzaken/)
- Raji, I. D., Smart, A., White, R. N., Mitchell, M., Gebru, T., Hutchinson, B., Smith-Loud, J., Theron, D., & Barnes, P. (2020). Closing the AI accountability gap: Defining an end-to-end framework for internal algorithmic auditing. In *Proceedings of the 2020 Conference on Fairness, Accountability, and Transparency* (pp. 33–44). [https://doi.org/10.1145/3351095.3372873](https://doi.org/10.1145/3351095.3372873)
- Skitka, L. J., Mosier, K. L., & Burdick, M. (1999). Does automation bias decision-making? *International Journal of Human-Computer Studies, 51*(5), 991–1006. [https://doi.org/10.1006/ijhc.1999.0252](https://doi.org/10.1006/ijhc.1999.0252)。本次只核對書目；文中為概念轉述。
- Strathern, M. (1997). 'Improving ratings': Audit in the British University system. *European Review, 5*(3), 305–321. [https://doi.org/10.1002/(SICI)1234-981X(199707)5:3<305::AID-EURO184>3.0.CO;2-4](https://doi.org/10.1002/(SICI)1234-981X(199707)5:3<305::AID-EURO184>3.0.CO;2-4)
- Warmington, R. J., & Henderson, I. R. (2020, March 25). *Written evidence submitted by Second Sight Support Services Ltd* (POH0005). House of Commons. [官方網頁](https://committees.parliament.uk/writtenevidence/917/html/)
- Williams, W. (2025). *Post Office Horizon IT Inquiry report, Volume 1* (HC 1119). [官方 PDF](https://www.postofficehorizoninquiry.org.uk/sites/default/files/2025-07/Post%20Office%20Horizon%20IT%20Inquiry%20Final%20Report%20Volume%201_0.pdf)。引用範圍：第 3 章人身影響，§3.12。
- Youth Justice and Criminal Evidence Act 1999, c. 23, s. 60. [legislation.gov.uk](https://www.legislation.gov.uk/ukpga/1999/23/section/60)