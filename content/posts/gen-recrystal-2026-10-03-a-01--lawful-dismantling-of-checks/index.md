+++
title = "合法地拆掉自己的煞車：報酬時間差、例外狀態與受託人義務看不到的地方"
date = "2026-10-03T04:56:01+08:00"
author = "么么陸"
draft = false
isCJKLanguage = true
description = "一家製造複雜系統的公司，要靠許多看不見的東西維持安全：多一顆感測器、多一輪測試、資深工程師可以對時程說不、品質人員的異議能一路送到董事會。這些東西有個共同點：它們的成本每一季都看得到，它們的價值只有在事故沒發生時才存在，而沒發生的事故不會出現在財報上。本文把這類為了吸收錯誤而保留、平時看似閒置的能力稱為糾錯緩衝（Slack）。"
tags = [
    "分析論述", # term:AnalyticalEssay
    "AI 經濟與社會", # term:AiEconomics
    "例外狀態", # term:StateOfException
    "受託人義務", # term:FiduciaryDuty
    "糾錯緩衝", # term:Slack
    "判斷", # term:Judgment
    "代理理論", # term:AgencyTheory
    "商業判斷法則", # term:BusinessJudgmentRule
  ]
series = ["拆掉自己的煞車：組織如何在合理、合法甚至高效的名義下，失去發現自身錯誤的能力"]
term_exclude = ["Claim", "Guidelines", "Intent", "Screening"]
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

一家製造複雜系統的公司，要靠許多看不見的東西維持安全：多一顆感測器、多一輪測試、資深工程師可以對時程說不、品質人員的異議能一路送到董事會。這些東西有個共同點：它們的成本每一季都看得到，它們的價值只有在事故沒發生時才存在，而沒發生的事故不會出現在財報上。本文把這類為了吸收錯誤而保留、平時看似閒置的能力稱為**糾錯緩衝**（Slack） <!-- term:Slack -->。

> [!IMPORTANT]
> **糾錯緩衝** <!-- term:Slack --> (Slack): 用以吸收極端風險的安全冗餘與隱性工藝存量。 <!-- anchor:Slack -->


本文要回答的問題是：一個組織的決策者，在什麼條件下會理性地消耗糾錯緩衝 <!-- term:Slack -->、拆除內部審查，而且整個過程可以完全合法？法律與公司治理現有的工具，能抓到其中哪些部分，又必然漏掉哪些部分？

這個問題影響的不只是投資人。第一線工程師需要知道自己的異議在什麼結構下會被吸收掉；董事與稽核人員需要知道「有程序、有紀錄」為什麼不等於「有在看」；採購新技術的技術主管需要分辨，什麼時候「不跟上就會被淘汰」是真實的競爭**判斷**（Judgment） <!-- term:Judgment -->，什麼時候它被用來暫停正常的審查。讀完本文，讀者應能用三個問題檢查一個組織：決策者的報酬在什麼時間點兌現，損害又在什麼時間點浮現？誰有權宣告「這次不走正常程序」？第一線的異議有沒有一條不經過任何單一主管的路，能送到負有監督義務的人手上？

> [!IMPORTANT]
> **判斷** <!-- term:Judgment --> (Judgment): 面對規則未覆蓋或情境已改變時，根據脈絡評估決定的實質後果。 <!-- anchor:Judgment -->


本文的答案分四步。第一步，用**代理理論**（Agency Theory） <!-- term:AgencyTheory -->與一個本文設定的報酬模型，說明報酬與後果的時間差如何讓消耗糾錯緩衝 <!-- term:Slack -->成為個人理性，以及常見的**追回條款**（Clawback） <!-- term:Clawback -->為什麼不一定能翻轉這個結論。第二步，處理第二條路線：外部敘事與資本市場的壓力，讓管理層宣告**例外狀態**（State Of Exception） <!-- term:StateOfException -->，暫停平時的審查。第三步，指出兩條路線的共同結構，並用圖論中的**割點**（Cut Vertex） <!-- term:CutVertex -->把「異議能不能送達」寫成可以檢查的條件。第四步，對照美國德拉瓦州公司法與證券法規，看法律實際能抓到什麼。

> [!IMPORTANT]
> **代理理論** <!-- term:AgencyTheory --> (Agency Theory): 以委託人與代理人間的利益衝突為核心，解釋經理人誘因如何偏離組織長期存續。 <!-- anchor:AgencyTheory -->
> **追回條款** <!-- term:Clawback --> (Clawback): 薪酬契約或法規中的條款，規定在事後發現財務重編、重大過失或系統性風險造成損害時，組織有權追回先前發放給高階經理人之績效獎金或股權。 <!-- anchor:Clawback -->
> **例外狀態** <!-- term:StateOfException --> (State Of Exception): 主權者中止常態規範的決斷時刻；在組織內用來架空架構審查與合規煞車。 <!-- anchor:StateOfException -->
> **割點** <!-- term:CutVertex --> (Cut Vertex): 資訊與責任傳遞網路中一旦被插入或破壞，即導致子圖孤立、反饋中斷的關鍵節點。 <!-- anchor:CutVertex -->


證據分三類。波音 737 MAX 的資料取自美國眾議院委員會報告、**美國證券交易委員會**（SEC） <!-- term:SecuritiesAndExchangeCommission -->的處分與德拉瓦州法院的裁定；電信泡沫取自 SEC 處分與事後研究；2025 年人工智慧產業的投資與採購協議只取自公司公告，本文不主張其中任何一筆違法。報酬模型與組織圖模型是本文設定的簡化，用來檢查推導，不是對任何公司的估計。個案用來說明條件是否出現，不能用來推斷當事人的動機。

> [!IMPORTANT]
> **美國證券交易委員會** <!-- term:SecuritiesAndExchangeCommission --> (Securities And Exchange Commission): 美國聯邦政府負責監督證券市場、執行聯邦證券法規並保護投資人權益的獨立法定主管機關。 <!-- anchor:SecuritiesAndExchangeCommission -->


## 分析

### 一、報酬與後果的時間差：拆除糾錯緩衝何時成為個人理性

代理理論 <!-- term:AgencyTheory -->處理的是所有權與經營權分離後的利益衝突：股東委託經理人經營，但經理人的努力與決策不能被股東完整**觀察**（Observation） <!-- term:Observation -->，雙方利益也不一致（Jensen & Meckling, 1976）。常見的處方是把經理人的報酬與股價綁在一起，例如發放**股票期權**（Stock Options） <!-- term:StockOptions -->，讓經理人「變成股東」。這個處方有一個隱含前提：股價會反映經理人決策的長期後果。

> [!IMPORTANT]
> **觀察** <!-- term:Observation --> (Observation): 規格工作流中對指定環境、介面或系統版本現況進行檢查所獲得的事實紀錄。 <!-- anchor:Observation -->
> **股票期權** <!-- term:StockOptions --> (Stock Options): 將經理人薪酬與股價綁定的激勵工具；當歸屬週期短於系統崩潰潛伏期時，會打開跨期套現空間。 <!-- anchor:StockOptions -->


糾錯緩衝 <!-- term:Slack -->正好是這個前提最弱的地方。設想一家公司把多餘感測器的成本、獨立測試的工時與資深工程師的審查時間砍掉，下一季的毛利會上升；這些糾錯緩衝 <!-- term:Slack -->少了之後，事故風險上升多少，外部投資人在短期內幾乎看不到。只要報酬在損害浮現之前就能兌現，經理人個人承擔的後果就和公司承擔的後果分開了。

這個機制有一個經常被引用的經濟學前例，但引用時要分清楚它說了什麼。Akerlof 與 Romer（1993）在〈洗劫：以破產牟利的經濟學地下世界〉中研究的是美國儲貸危機等案例：在**有限責任**（Limited Liability） <!-- term:LimitedLiability -->、政府擔保的借款與寬鬆的會計監管同時存在時，業主可以「付給自己比公司價值更多的錢，然後讓公司對債務違約」。他們的主角是能直接從公司抽取資金的業主，靠的是政府擔保讓債權人不必監督。本文把同一個邏輯延伸到經理人的股權報酬上，這是本文推論，不是兩位作者的主張；延伸成立需要下面三個前提同時為真：

> [!IMPORTANT]
> **有限責任** <!-- term:LimitedLiability --> (Limited Liability): 將個人賠償上限隔離於企業破產損失之外的法人制度。 <!-- anchor:LimitedLiability -->


1. **前提一（不可觀察 <!-- term:Observation -->）**：消耗糾錯緩衝 <!-- term:Slack -->所增加的風險，在報酬兌現前不會被市場定價。
2. **前提二（先兌現）**：經理人能在損害浮現前兌現大部分報酬。
3. **前提三（後果不回頭）**：損害浮現時，經理人個人負擔的追回或制裁，期望值小於他兌現的收益。

這三個前提是讓這個機制成立的一組充分條件，不是唯一的路徑。即使風險已被市場定價，只要削減帶來的成本節省大於被定價的風險，削減仍可能對股東與經理人都有利，那是另一個問題。本文關心的是三個前提同時成立的情形：市場看不見風險，股價就會因削減糾錯緩衝 <!-- term:Slack -->而上升；報酬能先兌現，就有時間差可以利用；追回與制裁不夠重，消耗糾錯緩衝 <!-- term:Slack -->就划算。前兩個前提是事實問題，要逐案查證；第三個前提是制度設計問題，下面用一個簡單模型說明它比直覺更難滿足。

#### 追回條款為什麼不一定能翻轉結論

常見的修補是追回條款 <!-- term:Clawback -->：事後發現問題，就把多發的報酬收回。直覺上，追回期間只要夠長、涵蓋損害浮現的時間，經理人就沒有誘因。這個直覺漏了一件事：追回能不能發生，取決於條款的觸發條件與發現的機率，而不只取決於期間長短。

本文設定以下量（所有量都是經理人選擇「消耗糾錯緩衝 <!-- term:Slack -->」相對於「維持糾錯緩衝 <!-- term:Slack -->」的差額，以同一貨幣單位計）：

- $G > 0$：因消耗糾錯緩衝 <!-- term:Slack -->而在兌現期內多得的報酬。
- $p_w \in [0,1]$：損害在追回期間內浮現的機率。
- $p_t \in [0,1]$：損害浮現後，追回條款 <!-- term:Clawback -->真的被觸發的機率。它由條款的觸發條件決定，例如條款若只在財報重編時觸發，而損害是安全事故，$p_t$ 就接近 0。
- $m \ge 0$：觸發時追回的金額是 $G$ 的幾倍。只收回多發部分時 $m = 1$。
- $p_s \in [0,1]$ 與 $S \ge 0$：個人另受制裁的機率與金額。

經理人的期望淨得為：

$$
U = G - p_w \, p_t \, m \, G - p_s \, S
$$

這條式子的推論只有一步。因為 $G > 0$，$U \le 0$ 若且唯若 $p_w \, p_t \, m + p_s S / G \ge 1$。若沒有另外的個人制裁（$p_s S = 0$），條件簡化為 $p_w \, p_t \, m \ge 1$。在這個簡化情形下，只收回多發部分（$m = 1$）的條款，只有在「損害一定在期間內浮現、而且一定觸發」時（$p_w = p_t = 1$）才剛好打平；只要任何一個機率小於 1，消耗糾錯緩衝 <!-- term:Slack -->的期望淨得仍是正的。這與 Becker（1968）在〈犯罪與懲罰：一個經濟學方法〉中的基本結論一致：嚇阻要求的是期望懲罰，也就是被抓到的機率乘以懲罰，不能低於違規的收益。

滿足的例子（設想）：追回期間十年、涵蓋損害浮現時間，$p_w = 1$；條款以「重大安全事故」為觸發條件且由獨立委員會判定，$p_t = 0.8$；追回金額為多得報酬的兩倍，$m = 2$。則 $p_w p_t m = 1.6 \ge 1$，$U < 0$，消耗糾錯緩衝 <!-- term:Slack -->不划算。

違反的例子（設想）：同樣的期間與倍數，但條款只在財報重編時觸發。安全方面的糾錯緩衝 <!-- term:Slack -->被消耗不會導致財報重編，$p_t \approx 0$，於是 $U \approx G > 0$。期間再長也沒有用。

這個模型只檢查一條規則：在給定的機率與倍數下，期望淨得的正負。它不能告訴我們真實的 $p_w$、$p_t$ 是多少，也不能證明任何經理人做過這樣的計算。讀者可以用下面的程式自行驗證式子的推論，斷言會在條件被違反時失敗：

```python
# 本文設定：數值皆為設想，用來檢查式子，不是任何公司的估計
def expected_net(gain, p_window, p_trigger, multiple, p_sanction=0.0, sanction=0.0):
    for p in (p_window, p_trigger, p_sanction):
        if not 0.0 <= p <= 1.0:
            raise ValueError("probability out of range")
    if gain <= 0 or multiple < 0 or sanction < 0:
        raise ValueError("invalid magnitude")
    return gain - p_window * p_trigger * multiple * gain - p_sanction * sanction

G = 100.0
# 追回期間在損害浮現前結束：期望淨得等於全部收益
assert expected_net(G, 0.0, 1.0, 1.0) == G
# 期間夠長、只收回多發部分、觸發機率 0.5：仍為正
assert expected_net(G, 1.0, 0.5, 1.0) > 0
# 嚇阻門檻：p_w * p_t * m 恰為 1 時打平，略低於 1 時仍為正
assert abs(expected_net(G, 1.0, 0.5, 2.0)) < 1e-9
assert expected_net(G, 1.0, 0.5, 1.99) > 0
assert expected_net(G, 1.0, 0.8, 2.0) < 0
# 觸發條件不涵蓋此類損害（p_t = 0）：倍數再高也無效
assert expected_net(G, 1.0, 0.0, 10.0) == G
# 另有個人制裁時，門檻為 p_w * p_t * m + p_s * S / G
assert abs(expected_net(G, 1.0, 0.5, 1.0, p_sanction=0.5, sanction=100.0)) < 1e-9
assert expected_net(G, 1.0, 0.5, 1.0, p_sanction=0.6, sanction=100.0) < 0
```

把這個模型對照美國現行規則，可以看到一個具體缺口。SEC 在 2022 年依《陶德－法蘭克法案》第 954 條通過的追回規則（Rule 10D-1），要求上市公司在需要重編財報時，追回現任與前任高階主管在之前三個完整會計年度內多領的激勵報酬（SEC, 2022a）。它的觸發條件是會計重編，追回金額以「依重編後數字本不該領的部分」為限。安全事故本身不觸發這條規則；只有同一事件也導致財報必須重編時，它才可能適用。把這個範圍對應到上面的模型是本文的設定：對單純的安全糾錯緩衝 <!-- term:Slack -->消耗，$p_t$ 接近 0；而規則追回的「重編後本不該領的部分」，與模型中的 $G$ 不一定相同。該規則本來就是為財報不實設計的，不是為安全裕度設計的；它沒有失靈，只是不在這個問題的範圍內。

### 二、例外狀態：外部敘事如何暫停內部審查

第一條路線靠的是個人報酬的時間差，第二條路線不需要任何人存心提取價值。它從組織外部開始：資本市場、分析師與顧問共同形成一種敘事，認為某項技術將決定企業存亡，不立刻投入就會被淘汰。管理層接受這個敘事後，最有效的動作往往不是做錯某個採購決定，而是宣告平時的審查程序不適用於這次。

德國法學家 Carl Schmitt 在 1922 年的《政治神學》開篇寫道：「主權者就是決定例外狀態 <!-- term:StateOfException -->的人」（Schmitt, 1922/2005）。本文借用例外狀態 <!-- term:StateOfException -->這個概念，只取它的結構意義：在一個組織裡，誰有權宣告某件事「不適用正常程序」，誰就在那件事上握有不受審查的權力。這是類比，不是說企業管理層等同於國家主權者。

本文設定的正常程序是：一項重大技術採購要經過**概念驗證**（Proof Of Concept） <!-- term:ProofOfConcept -->、架構審查、資安與法遵審查、財務與內部稽核。這些關卡的工作就是提出物理限制與成本質疑：系統在資料分布改變時會怎樣、敏感資料能否送到第三方、每次呼叫的成本能否被產出的價值覆蓋、供應商改價或停止服務時有沒有退路。宣告例外之後，本文設想的典型做法是成立直屬執行長的專案小組、把專案標示為「試點」或「實驗」以避開完整審查，並把提出上述問題的人描述成阻礙創新的人。依本文推論，這些做法每一項都可以是合法的，問題在於它們合在一起，讓那些關卡在這件事上失去否決力。

> [!IMPORTANT]
> **概念驗證** <!-- term:ProofOfConcept --> (Proof Of Concept): 在規模化採購前驗證技術可行性與邊界條件的試驗。 <!-- anchor:ProofOfConcept -->


例外狀態 <!-- term:StateOfException -->最常悄悄替換掉的，是評估依據本身。一個在受控展示中運作良好的方案，不等於在生產資料、真實負載與長期維護下仍然可用；經過挑選的示範可以說服不熟悉技術細節的董事，卻回答不了上述任何一個問題。依本文推論，當評估依據從「在生產條件下的試驗結果」變成「展示的印象」，審查關卡即使名義上還在，也已經沒有可以否決的對象。

#### 外部敘事的燃料：融資結構與交叉交易

外部壓力有它的金融來源。經濟學家 Hyman Minsky 把經濟單位依現金流與債務的關係分成三類：避險型融資的營運現金流足以支付本金與利息；投機型融資只夠付利息，本金要靠展期；**龐氏融資**（Ponzi Financing） <!-- term:PonziFinancing -->的營運現金流連利息都不夠，必須靠繼續借錢或出售資產（Minsky, 1992）。這是一個融資結構的分類，不是詐欺的指控。當一個產業有大量單位處在後兩類時，它的存續就依賴資產價格持續上升與新資金持續流入，而維持這種流入需要一個讓人相信未來收入會追上支出的敘事。

> [!IMPORTANT]
> **龐氏融資** <!-- term:PonziFinancing --> (Ponzi Financing): 營運現金流不足以支付利息，存續完全依賴資產價格上漲或外部信貸。 <!-- anchor:PonziFinancing -->


2025 年的人工智慧產業出現幾筆受到廣泛討論的協議。**輝達**（Nvidia） <!-- term:Nvidia -->與 OpenAI 在 9 月宣布意向書，輝達 <!-- term:Nvidia -->將「隨每一吉瓦部署，逐步投資至多 1,000 億美元」，而 OpenAI 將部署輝達 <!-- term:Nvidia -->的系統（OpenAI & NVIDIA, 2025）。**超微**（Amd） <!-- term:Amd -->與 OpenAI 在 10 月宣布，OpenAI 部署超微 <!-- term:Amd -->的 GPU，超微 <!-- term:Amd -->則發給 OpenAI 一份可認購至多 1.6 億股的認股權證，依部署與里程碑分批生效（OpenAI & AMD, 2025）。CoreWeave 在 9 月的申報文件中揭露，輝達 <!-- term:Nvidia -->同意在一定條件下購買其未售出的剩餘算力，期限到 2032 年（CoreWeave, 2025）。這些協議的共同形狀是：供應商投資或擔保客戶，客戶再向供應商採購。

> [!IMPORTANT]
> **輝達** <!-- term:Nvidia --> (Nvidia): 全球領先的圖形處理器與人工智慧硬體加速晶片設計製造商。 <!-- anchor:Nvidia -->
> **超微** <!-- term:Amd --> (Amd): 全球高階微處理器與圖形處理器半導體設計製造商。 <!-- anchor:Amd -->


這個形狀本身不構成違法，也不必然構成虛增營收。會計上的分界在於交易有沒有經濟實質。SEC 在 2002 年處分 Dynegy 時指出，其能源「**迴圈交易**（Round-Tripping） <!-- term:RoundTripping -->」（Round-Tripping）缺乏經濟實質（SEC, 2002）。另一方面，供應商付給客戶的對價，依美國會計準則的收入認列規定，原則上要沖減供應商的營收，除非那筆錢換到的是可以區分的商品或服務（KPMG, 2023）。換句話說，判斷 <!-- term:Judgment -->要看每一筆交易的條款與實際履約，單憑公告既不能證明有問題，也不能證明沒有問題。本文只用這些協議說明外部敘事有真實的資金結構支撐，不對它們的會計處理下判斷 <!-- term:Judgment -->。

> [!IMPORTANT]
> **迴圈交易** <!-- term:RoundTripping --> (Round-Tripping): 同一筆資本在關聯實體間來回認列、製造虛增營收的交易結構。 <!-- anchor:RoundTripping -->


歷史上有一個已經走完的對照。1990 年代末的電信擴張中，Qwest 與 Global Crossing 等公司彼此交換光纖容量的不可撤銷使用權（IRU）。SEC 指控 Qwest 以多種手法合計不當認列超過 38 億美元的收入，其中一種是把這類交換的收入在當期一次認列，而依會計準則應在合約期間內分攤認列或根本不認列；Qwest 未承認也未否認指控，以 2.5 億美元民事罰款和解（SEC, 2004）。Global Crossing 則因在財報的管理階層討論中沒有充分揭露這類交換而受處分（SEC, 2005）。同一時期，替電信業者撰寫推薦報告的明星分析師 Jack Grubman，在 2003 年的研究分析師全面和解中被終身禁止從事證券業，並支付 1,500 萬美元（SEC, 2003）。泡沫破裂後，Litan（2002）在布魯金斯學會的政策簡報中估計，北美長途線路的容量被使用的不超過 2%，股東損失約 2 兆美元。這段歷史的價值在於它分開了兩件事：容量過度建設是一場預期錯誤，錯誤的會計處理與利益衝突的推薦才是可追究的違規。

#### 例外狀態對採購方的代價

對大多數企業而言，它們不在上述交叉交易之中，而是在最外圈：被要求簽下長期採購、證明自己「有在轉型」的客戶。這時例外狀態 <!-- term:StateOfException -->造成的損害通常不是一筆壞帳，而是審查缺席時悄悄累積的風險。2023 年，三星的工程師把內部原始碼上傳到 ChatGPT，三星隨後限制員工在公司裝置與內部網路上使用生成式人工智慧工具（Gurman, 2023）。這個事件本身不涉及例外狀態 <!-- term:StateOfException -->的宣告，本文用它說明一件事：新工具的使用一旦先於資料外流的審查，損害可以在任何人做出「錯誤決定」之前就發生。依本文推論，例外狀態 <!-- term:StateOfException -->還會從另一個方向製造同樣的風險：由上而下推行的工具若不合第一線的實際需要，員工可能一邊讓已付費的授權閒置，一邊自行使用未經核准的外部工具，或用未經架構審查的自動化流程串接系統；風險於是移到沒有人監看的地方，而採購報表上只會看到「已導入」。

### 三、兩條路線的共同結構：異議的送達路徑

兩條路線的起點不同。第一條是清醒的提取者，他知道糾錯緩衝 <!-- term:Slack -->有價值，只是那個價值不由他承擔；第二條是被敘事推著走的管理層，他可能真心相信不跟上就會失敗。但兩者對組織做的事情相同：讓最清楚風險的人，無法把風險送到有權停下來的人手上。

這件事可以寫成一個可以檢查的條件。把組織畫成一張圖：每個人或單位是一個節點，「A 的意見能直接送到 B」是一條連線。第一線工程師是起點 $F$，負有監督義務的董事會（或其獨立委員會）是終點 $B$。在圖論中，若移除某個節點會讓兩個原本相連的節點不再相連，該節點就是它們之間的割點 <!-- term:CutVertex -->。在組織裡，割點 <!-- term:CutVertex -->就是一個「只要他不轉達，異議就到不了」的位置。

社會學家 Ronald Burt 用**結構洞**（Structural Holes） <!-- term:StructuralHoles -->描述兩群原本不相連的人之間的空隙：站在空隙中間、同時連著兩邊的人，能控制哪些資訊從一邊流到另一邊（Burt, 2004）。割點 <!-- term:CutVertex -->是這個位置在送達問題上的極端形式：唯一的轉達者。

> [!IMPORTANT]
> **結構洞** <!-- term:StructuralHoles --> (Structural Holes): 兩個孤立群落之間的資訊斷層，佔位者可抽取話語權租金。 <!-- anchor:StructuralHoles -->


本文要檢查的條件是：

> 對任何單一中間節點 $v$（$v \ne F, B$），從圖中移除 $v$ 之後，$F$ 仍能到達 $B$。

這裡的連線有方向，而且條件先假設 $F$ 本來就能到達 $B$。依 Menger（1927）的定理，當 $F$ 與 $B$ 之間沒有直接連線時，這個條件等價於：$F$ 到 $B$ 之間至少有兩條除端點外不共用任何節點的路徑。它的意思很樸素：任何一個主管、任何一套看板系統，單獨都無法攔下異議。若 $F$ 與 $B$ 之間有直接連線，移除任何中間節點都切不斷它，條件自動成立，但這時只靠一條路，需要另外檢查那條直接連線本身是否可靠。

滿足的例子（設想）：工程師的異議可以經「工程主管 → 事業部主管 → 董事會」送達，也可以經一條由董事會安全委員會直接管理的通報管道送達。兩條路不共用任何中間節點，移除任何一個主管或任何一個系統，另一條路仍在。

違反的例子（設想）：公司設了通報管道，但通報先送給事業部主管做初步篩選，再由他決定是否上呈。圖上看起來有兩條路，實際上兩條路都經過事業部主管，他就是割點 <!-- term:CutVertex -->。

下面的程式把這個條件寫成檢查。它只檢查連線的結構，不能檢查通報管道有沒有人用、使用的人會不會被報復、收到的人會不會讀；這些是結構以外的問題，下一節的個案會碰到它們。

```python
# 本文設定：組織圖為設想；邊表示「意見能直接送到」
def reachable(edges, src, dst, removed=None):
    seen, stack = {src}, [src]
    while stack:
        node = stack.pop()
        if node == dst:
            return True
        for a, b in edges:
            if a == node and b != removed and b not in seen:
                seen.add(b)
                stack.append(b)
    return False

def single_points_of_filtering(edges, src, dst):
    if not reachable(edges, src, dst):
        raise ValueError("dissent cannot reach the board at all")
    nodes = {n for e in edges for n in e} - {src, dst}
    return sorted(v for v in nodes if not reachable(edges, src, dst, removed=v))

chain = [("F", "Eng"), ("Eng", "Div"), ("Div", "B")]
with_direct_line = chain + [("F", "Line"), ("Line", "B")]
line_screened_by_div = chain + [("F", "Line"), ("Line", "Div")]

assert single_points_of_filtering(chain, "F", "B") == ["Div", "Eng"]
assert single_points_of_filtering(with_direct_line, "F", "B") == []
# 管道存在但經事業部主管篩選：仍有割點
assert single_points_of_filtering(line_screened_by_div, "F", "B") == ["Div"]
# 異議根本送不到：不能當成「沒有割點」
try:
    single_points_of_filtering([("F", "Eng")], "F", "B")
    raise AssertionError("unreachable board must not pass")
except ValueError:
    pass
```

三個設想的組織圖得到三種結果。單純的層級鏈中，工程主管與事業部主管都是割點 <!-- term:CutVertex -->，任何一人不轉達，異議就停住。加上一條由董事會直接管理的管道後，割點 <!-- term:CutVertex -->消失。但若這條管道先送到事業部主管，它在圖上就不是第二條路，事業部主管仍是割點 <!-- term:CutVertex -->。最後一個檢查防止一種誤讀：異議根本送不到董事會時，「找不到割點 <!-- term:CutVertex -->」不代表安全。

#### 個案：波音 737 MAX 呈現了哪些條件

波音 737 MAX 在 2018 年 10 月與 2019 年 3 月兩度墜毀，共 346 人罹難。美國眾議院運輸與基礎設施委員會在 2020 年的調查報告，記錄了幾項與本文條件相關的事實（U.S. House Committee on Transportation and Infrastructure, 2020）：

- 一開始的**設計決定**（Design Decision） <!-- term:DesignDecision -->就帶著時程與成本的壓力。為了回應空中巴士 A320neo，波音沒有開發全新機型，而是改良既有的 737 NG，「不必從頭開始，節省時間、資源與成本」。較大的 CFM LEAP-1B 發動機必須裝得更前、更高才能保持離地間距，這改變了飛機的空氣動力特性；**機動特性增強系統**（MCAS） <!-- term:ManeuveringCharacteristicsAugmentationSystem -->就是為了抵銷飛機在大攻角時機頭上仰的傾向而加入的（pp. 38–43）。
- 原始設計的 MCAS 只依賴單一攻角感測器的讀數。
- 波音與西南航空的合約約定，若飛行員因任何原因無法在舊型 737 與 737 MAX 之間互換駕駛，波音須就每架交付的飛機支付 100 萬美元。這讓「不需要模擬機訓練」成為有價格的商業承諾。
- 美國聯邦航空總署把部分認證工作授權給波音自己的員工執行，這些授權代表「應該代表聯邦航空總署的利益」。2016 年的一項內部調查中，39% 回覆的授權代表認為曾受到「不當壓力」，29% 擔心通報的後果。
- 2013 年，一位工程師提議比照 787 加裝合成空速指示，被管理層以成本與可能觸發模擬機訓練要求為由拒絕（p. 18）。

> [!IMPORTANT]
> **設計決定** <!-- term:DesignDecision --> (Design Decision): 在技術限制與方案取捨下經負責者審查並接受的具體架構或實作選擇。 <!-- anchor:DesignDecision -->
> **機動特性增強系統** <!-- term:ManeuveringCharacteristicsAugmentationSystem --> (Maneuvering Characteristics Augmentation System): 波音 737 MAX 為抵銷發動機前移造成的機頭上仰特性而引入的自動俯仰控制飛行控制軟體系統。 <!-- anchor:ManeuveringCharacteristicsAugmentationSystem -->


同一時期的財務面，Lazonick 與 Sakinç（2019）計算，波音從 2013 年第一季到 2019 年第一季花了 431 億美元買回自家股票，相當於同期淨利的 104%，另以股利發放 174 億美元。2004 年，時任執行長 Harry Stonecipher 曾對記者表示，他改變波音文化的意圖，就是讓公司「像一門生意那樣經營，而不是一家偉大的工程公司」（Catchpole, 2020 轉引）。事故後，執行長 Dennis Muilenburg 在 2019 年 12 月離職，沒有領取遣散費，但帶走超過 6,000 萬美元的股票與退休金權益（Josephs, 2020）；SEC 在 2022 年認定波音與 Muilenburg 在事故後發表誤導投資人的聲明，分別處以 2 億美元與 100 萬美元罰款（SEC, 2022b）。

這些事實支持什麼、不支持什麼，要分開說。它們支持：認證與工程審查的管道承受了商業壓力，「不需要模擬機訓練」被定價後成為設計約束，同期有大量資本分配給股東。它們不足以判斷 <!-- term:Judgment -->：這些股東分配是否排擠了新機型的開發，以及經理人的報酬是否在損害浮現前就已大量兌現。Muilenburg 帶走的權益是兩次墜機之後的數字，不能證明前提二；要檢驗前提二，需要事故前的實際交易紀錄。它們更不支持：任何特定主管為了兌現股票期權 <!-- term:StockOptions -->而刻意削減安全。本文第一節的模型只說明那樣的誘因在什麼條件下存在；個案顯示的是異議與認證路徑承受壓力，以及（下一節會提到的）董事會缺少直接監督安全的機制，而不是當事人的動機。把結果相似當成動機相同，正是事後檢討最常犯的錯。

#### 割點之外：權力、知識與責任落在哪裡

割點 <!-- term:CutVertex -->條件回答的是「異議能不能送達」。還有一個相關的結構問題：有權做決定的人、最清楚現場狀況的人，與出事時個人要負責的人，是不是同一群人。依本文推論，三者分離時，決策者不必知道風險，知道風險的人沒有權停下來，而簽名的人承擔後果；每一方在自己的位置上都可以是理性的。波音的授權代表是一個具體的例子：他們受僱於製造商，卻「應該代表」監理機關執行認證，而近四成的人表示感到不當壓力。有一種常見的主張把這個問題寫成一條「權力、知識與責任必須收斂於同一決策實體」的律則，這樣的強版本在大型組織中無法做到，分工本來就會把三者拆開。本文建議較弱的版本：三者可以分開，但對每一項攜帶重大風險的決定，都要能指出是誰在知情的情況下做了決定、他從哪裡得知現場的反對意見，以及他為此負什麼責任。這三個問題答不出來的決定，就是在結構上沒有人負責的決定。

### 四、法律能抓到什麼：受託人義務的形式與實質

經理人與董事對公司負有**受託人義務**（Fiduciary Duty） <!-- term:FiduciaryDuty -->，通常分成**注意義務**（Duty Of Care） <!-- term:DutyOfCare -->與**忠實義務**（Duty Of Loyalty） <!-- term:DutyOfLoyalty -->。德拉瓦州法院處理董事決策時，先推定**商業判斷法則**（Business Judgment Rule） <!-- term:BusinessJudgmentRule -->適用：董事在知情、善意、無利益衝突下做的決定，法院不事後評斷其好壞。要讓董事為監督失職負個人責任，現行標準來自三個判決的累積。

> [!IMPORTANT]
> **受託人義務** <!-- term:FiduciaryDuty --> (Fiduciary Duty): 經理人對公司與利害關係人負有的注意與忠實義務，是古典公司治理中代理人正當性的法理基礎。 <!-- anchor:FiduciaryDuty -->
> **注意義務** <!-- term:DutyOfCare --> (Duty Of Care): 受託人須以合理謹慎作成知情決策的義務。 <!-- anchor:DutyOfCare -->
> **忠實義務** <!-- term:DutyOfLoyalty --> (Duty Of Loyalty): 受託人不得將個人利益置於公司利益之上的義務。 <!-- anchor:DutyOfLoyalty -->
> **商業判斷法則** <!-- term:BusinessJudgmentRule --> (Business Judgment Rule): 推定經理人在知情且善意時免於個人賠償；形式合規常被用來對抗實質的安全與審計審查。 <!-- anchor:BusinessJudgmentRule -->


1996 年的 Caremark 案確立董事有建立資訊與通報系統的義務，但只有「持續或系統性的監督失職」才足以證明欠缺善意（In re Caremark, 1996）。2006 年的 Stone v. Ritter 進一步說明，這種責任屬於忠實義務 <!-- term:DutyOfLoyalty -->，原告必須證明董事知情或有意識地漠視，一般的疏忽不夠（Stone v. Ritter, 2006）。2019 年的 Marchand v. Barnhill 涉及冰淇淋製造商 Blue Bell 的李斯特菌汙染，法院認定食品安全對這家公司是「攸關存亡的核心任務」，董事會對此完全沒有監督機制，原告的請求可以進入審理（Marchand v. Barnhill, 2019）。

這條線在 2021 年延伸到波音。德拉瓦州衡平法院在股東代位訴訟中，駁回董事會撤銷訴訟的請求，理由之一是「董事會沒有任何委員會負有直接監督飛機安全的責任」（In re The Boeing Company Derivative Litigation, 2021）。這是起訴階段的裁定，不是最終的責任認定；案件後來以 2.375 億美元和解，款項付給波音公司，主要由董事責任保險支應（Lieff Cabraser, 2022）。值得注意的是波音自己的回應：兩次墜機之後，董事會在 2019 年 8 月核准成立常設的航太安全委員會，負責監督飛機從設計、製造到營運與維修的安全（The Boeing Company, 2019）。這是一種可能的監督機制：在董事會層級設一個專門負責安全的單位。但委員會存在，不等於滿足割點 <!-- term:CutVertex -->條件；它是否讓第一線的異議有了不經管理層的第二條路，取決於委員會的資訊從哪裡來，公告只說明它的成立與職責，無法回答這一點。

把這條線對照本文的兩條路線，可以看出法律抓得到的範圍：

- **董事會對攸關存亡的風險缺少監督機制**：抓得到。Marchand 案是完全沒有食品安全的監督機制；波音案的起訴階段裁定則著眼於董事會沒有直接監督飛機安全的委員會。
- **有機制但被有意識地無視**：抓得到，但原告要證明「知情或有意識地漠視」，舉證很難。
- **有機制、有紀錄、程序完備，只是每個程序都在壓力下讓步**：很難抓到。機制效果不佳本身不足以證明欠缺善意；原告必須證明董事知道程序已失去作用，或看到警訊卻有意識地不處理。這正是例外狀態 <!-- term:StateOfException -->的典型形狀：審查會議開了，簽名蓋了，只是審查者在那件事上沒有否決力，而程序的完備讓「明知」更難被證明。
- **合法地消耗糾錯緩衝 <!-- term:Slack -->以提高短期獲利**：在商業判斷法則 <!-- term:BusinessJudgmentRule -->下通常是受保護的經營決定。要主張它構成浪費，原告須證明交易「如此一面倒，以致沒有任何具一般健全判斷 <!-- term:Judgment -->力的商人會認為公司得到了足夠的對價」（Brehm v. Eisner, 2000），門檻極高。

證券法提供另一條路，但它追究的是說了什麼，而不是做了什麼。SEC 對波音與 Muilenburg 的處分針對的是事故後誤導投資人的聲明。內線交易規則 Rule 10b5-1 允許內部人在不知悉重大非公開資訊時預先設定交易計畫，作為日後交易的抗辯。SEC 在 2022 年修正這條規則，另加冷靜期：董事與高階主管設定或修改計畫後，要等到 90 天後，或該季財報揭露後兩個營業日，兩者取較晚者，最長不超過 120 天，才能依計畫交易（SEC, 2022c）。這項修正縮小了「計畫設定後很快就開始賣出」的空間，但它管的是交易時點，不是經營決策本身。

所以，本文對「合法地拆掉煞車」的回答是：法律能追究沒有煞車、明知煞車失靈卻不處理，以及出事後謊稱煞車完好；它很難追究在每一道程序都存在、又沒有人能被證明「明知」的情況下，煞車被一點一點拆掉。這個空隙不是哪條法律寫錯了，而是受託人義務 <!-- term:FiduciaryDuty -->的法理本來就尊重經營判斷 <!-- term:Judgment -->，把個人責任保留給惡意與有意識的漠視；程序完備會提高證明這兩者的難度。這是本文對判決脈絡的詮釋。

## 反思

**事後的清晰。** 本文用波音與電信泡沫說明條件，但這兩個個案都是在結局已知後才被整理成因果故事的。在事故發生前，削減一項測試或採購一套工具，通常有正當的商業理由，而且大多數這樣的決定並沒有導致災難。任何以個案結局為起點的論證，都會高估當時決策者能看見的風險。本文的模型因此只回答「在什麼條件下誘因存在」，不回答「某人是否出於這個誘因行事」。

**糾錯緩衝 <!-- term:Slack -->也可能只是浪費。** 並非所有閒置能力都在保護組織。一個從未被使用的審查委員會、一份沒人讀的風險報告，也會消耗資源而不帶來保護。本文的論證不支持「所有削減都是掠奪」，它要求的是削減前能回答：這項糾錯緩衝 <!-- term:Slack -->吸收的是哪一類錯誤？拿掉它之後，那類錯誤改由什麼來吸收？答不出來時，削減就是在不知道代價的情況下消費它。

**例外有時是對的。** 競爭威脅可以是真實的，正常的審查程序也可能真的跟不上技術變化。問題不在於有沒有例外，而在於例外有沒有邊界。一個有邊界的例外，至少有名字、有範圍、有到期日，並且有一位具名的人為「這次不走正常程序」所承擔的風險負責。沒有這些的例外，與取消審查沒有分別。這是本文建議，沒有對應的實證研究支持其效果。

**多一條通報路徑也有代價。** 對不直接相連的兩端，割點 <!-- term:CutVertex -->條件等於要求至少兩條不共用節點的路，但每多一條路，接收端就要處理更多雜訊，第一線人員也可能用它繞過正常的管理溝通。圖模型無法衡量這些代價，也無法保證收到異議的人會認真處理。它只能排除一種最壞的結構：一個人就能讓異議消失。

**交叉投資的判斷 <!-- term:Judgment -->不能只靠形狀。** 供應商投資客戶、客戶向供應商採購的形狀，在電信泡沫裡伴隨過違規，但在許多產業也是正常的產業融資。本文刻意不對 2025 年的協議下判斷 <!-- term:Judgment -->，因為公告只揭露條款的輪廓。讀者若要判斷 <!-- term:Judgment -->，需要看的是每筆交易的會計處理、履約狀況與揭露，而不是交易的形狀。

## 實務對比

同樣面對「市場要求我們立刻導入某項技術」，組織可以有三種做法。下表比較它們在本文檢查問題上的表現；前兩欄是本文設定的典型，第三欄是本文建議，都不是任何組織的實際做法。

| 檢查問題 | 正常程序 | 無邊界的例外 | 有邊界的例外（本文建議） |
| :--- | :--- | :--- | :--- |
| 誰有權否決 | 架構、資安、法遵、稽核各自有否決權 | 專案小組直屬執行長，審查者只能提供意見 | 審查者保留否決權；例外只縮短時程，不取消關卡 |
| 評估依據 | 生產條件下的試驗與成本測算 | 受控展示與外部報告 | 由不隸屬專案小組的評估者，以生產資料條件試驗；列出資料流向、外部相依與停用後的退路 |
| 例外如何結束 | 不適用 | 沒有到期日，常態化 | 有到期日，到期須重新經過完整審查 |
| 風險由誰具名承擔 | 各關卡的負責人 | 無人具名，或由第一線簽核者承擔 | 宣告例外的主管具名承擔，並記錄在董事會議程 |
| 異議如何送達 | 經管理層與正式委員會 | 經專案小組，專案小組是割點 <!-- term:CutVertex --> | 另有一條由董事會委員會直接管理、不經專案小組的通報路徑 |

讀這張表時要注意，第三欄的每一項都可能被形式化地執行：到期日可以一再延長，具名承擔可以只是一個簽名。表格能比較的是結構，不能保證結構被認真對待。

追回條款 <!-- term:Clawback -->的設計也可以用同樣的方式比較。以第一節的符號，下表列出三種設想的條款，以及它們對「消耗安全方面的糾錯緩衝 <!-- term:Slack -->」這類損害的嚇阻力：

| 條款設計（設想） | $p_w$ | $p_t$ | $m$ | $p_w p_t m$ | 結論 |
| :--- | :-: | :-: | :-: | :-: | :--- |
| 只在財報重編時觸發，回溯三年 | 低 | 約 0 | 1 | 約 0 | 不嚇阻 |
| 安全事故觸發，回溯十年，只收回多發部分 | 1 | 0.6 | 1 | 0.6 | 不嚇阻 |
| 安全事故觸發，由獨立委員會判定，回溯十年，追回兩倍 | 1 | 0.8 | 2 | 1.6 | 嚇阻 |

表中數值都是設想，只用來示範式子如何區分三種設計。實際的觸發機率要看判定機制是否獨立、事故是否可歸因，這些無法從條款文字推出。

## 結論

一個組織可以在每個步驟都合法的情況下，拆掉保護它長期存續的機制。本文找到兩條路：報酬與後果的時間差，讓消耗糾錯緩衝 <!-- term:Slack -->成為個人理性；外部敘事帶來的例外狀態 <!-- term:StateOfException -->，讓平時的審查在特定事項上失去否決力。前者需要提取者，後者不需要任何人存心為惡。

兩條路的共同結果，是讓最清楚風險的人無法把風險送到有權停下來的人手上。這可以寫成一個可以檢查的結構條件：從第一線到監督者之間，不存在任何單一的割點 <!-- term:CutVertex -->。

現行法律對這個問題的覆蓋有清楚的邊界。受託人義務 <!-- term:FiduciaryDuty -->能追究「沒有監督系統」與「明知警訊而不處理」，證券法能追究「說了誤導的話」，追回規則能追究「財報不實」。對於在完備程序下逐步消耗、又無法證明有人明知的安全裕度，三者都很難觸及。在沒有其他個人制裁時，只收回多發部分的追回條款 <!-- term:Clawback -->，在發現機率小於 1 時仍留給提取者正的期望收益；要嚇阻，期望懲罰必須不低於收益。

可帶走的檢查只有三個問題：報酬什麼時候兌現，損害什麼時候浮現？誰能宣告「這次不走正常程序」，那個宣告何時到期、由誰具名承擔？第一線的異議，能不能在不經過任何單一主管的情況下，送到負有監督義務的人手上？

## 參考文獻 (References)

文中以（作者, 年份）標示出處，條目依作者字母排序。讀取日期為 2026-10-03。本文的報酬模型、組織圖與表中數值為明示設定，不是實測。標示「引用範圍」者，表示本文只引用該來源的指定部分；判決的引述以所列公開判決文本為準。

- Akerlof, G. A., & Romer, P. M. (1993). Looting: The economic underworld of bankruptcy for profit. *Brookings Papers on Economic Activity, 1993*(2), 1–73. [https://doi.org/10.2307/2534564](https://doi.org/10.2307/2534564)。引用範圍：有限責任 <!-- term:LimitedLiability -->、政府擔保與寬鬆監管下，業主付給自己比公司價值更多的錢後讓公司違約的機制。延伸到經理人股權報酬為本文推論。
- Becker, G. S. (1968). Crime and punishment: An economic approach. *Journal of Political Economy, 76*(2), 169–217. [https://doi.org/10.1086/259394](https://doi.org/10.1086/259394)。引用範圍：嚇阻取決於被抓機率與懲罰的乘積。
- The Boeing Company. (2019, September 25). *Boeing Chairman, President and CEO Dennis Muilenburg and Boeing Board of Directors reaffirm company's commitment to safety* [Press release]. [官方網頁](https://investors.boeing.com/investors/news/press-release-details/2019/Boeing-Chairman-President-and-CEO-Dennis-Muilenburg-and-Boeing-Board-of-Directors-Reaffirm-Companys-Commitment-to-Safety/default.aspx)。排序依 Boeing。引用範圍：航太安全委員會於 2019 年 8 月核准成立及其職責。
- Brehm v. Eisner, 746 A.2d 244 (Del. 2000). （本次未取得法院官方線上來源；判決文本轉載：[OpenCasebook](https://opencasebook.org/documents/853/)）。引用範圍：浪費的判斷 <!-- term:Judgment -->標準（p. 263）。
- Burt, R. S. (2004). Structural holes and good ideas. *American Journal of Sociology, 110*(2), 349–399. [https://doi.org/10.1086/421787](https://doi.org/10.1086/421787)。本次只核對書目；文中對結構洞 <!-- term:StructuralHoles -->的說明是概念轉述，不是引文。
- Catchpole, D. (2020, January 20). The forces behind Boeing's long descent. *Fortune*. [官方網頁](https://fortune.com/longform/boeing-737-max-crisis-shareholder-first-culture/)。文中 Stonecipher 談話轉引自本文，原始出處為 2004 年《芝加哥論壇報》，原文本次未能讀取。
- CoreWeave, Inc. (2025, September 15). *Form 8-K, Item 1.01*. U.S. Securities and Exchange Commission. [SEC EDGAR](https://www.sec.gov/Archives/edgar/data/1769628/000176962825000047/crwv-20250909.htm)
- Gurman, M. (2023, May 2). Samsung bans staff's AI use after spotting ChatGPT data leak. *Bloomberg News*. [官方網頁](https://www.bloomberg.com/news/articles/2023-05-02/samsung-bans-chatgpt-and-other-generative-ai-use-by-staff-after-leak)。全文經授權轉載版本讀取。
- In re Caremark International Inc. Derivative Litigation, 698 A.2d 959 (Del. Ch. 1996). （本次未取得法院官方線上來源；判決文本轉載：[OpenCasebook](https://opencasebook.org/documents/4105/)）
- In re The Boeing Company Derivative Litigation, C.A. No. 2019-0907-MTZ, 2021 WL 4059934 (Del. Ch. Sept. 7, 2021). （本次未取得法院官方線上來源；判決文本轉載：[OpenCasebook](https://opencasebook.org/documents/10577/)）。引用範圍：董事會沒有直接監督飛機安全的委員會（p. 74）；起訴階段裁定。
- Jensen, M. C., & Meckling, W. H. (1976). Theory of the firm: Managerial behavior, agency costs and ownership structure. *Journal of Financial Economics, 3*(4), 305–360. [https://doi.org/10.1016/0304-405X(76)90026-X](https://doi.org/10.1016/0304-405X(76)90026-X)
- Josephs, L. (2020, January 10). Boeing's fired CEO Muilenburg walks away with more than $60 million. *CNBC*. [官方網頁](https://www.cnbc.com/2020/01/10/ex-boeing-ceo-dennis-muilenburg-will-not-get-severance-payment-in-departure.html)
- KPMG. (2023, December 8). *Revenue accounting: Consideration payable to a customer*. KPMG IFRS Institute. [官方網頁](https://kpmg.com/us/en/articles/2023/revenue-accounting-consideration-payable-customer.html)。引用範圍：付給客戶的對價原則上沖減營收，除非換得可區分的商品或服務。
- Lazonick, W., & Sakinç, M. E. (2019, May 31). Make passengers safer? Boeing just made shareholders richer. *The American Prospect*. [官方網頁](https://prospect.org/environment/make-passengers-safer-boeing-just-made-shareholders-richer./)。引用範圍：2013 年第一季至 2019 年第一季的庫藏股與股利金額及其占淨利比例。
- Lieff Cabraser Heimann & Bernstein. (2022, March 22). *$237.5 Boeing derivative suit settlement granted final approval*. [官方網頁](https://www.lieffcabraser.com/2022/03/237-5-boeing-derivative-suit-settlement-granted-final-approval/)。原告律師事務所公告；和解金額、核准日期與保險支應的描述以此為據。
- Litan, R. E. (2002, December 1). *The telecommunications crash: What to do now?* (Policy Brief No. 112). Brookings Institution. [官方網頁](https://www.brookings.edu/articles/the-telecommunications-crash-what-to-do-now/)
- Marchand v. Barnhill, 212 A.3d 805 (Del. 2019). （本次未取得法院官方線上來源；判決文本轉載：[OpenCasebook](https://opencasebook.org/documents/7428/)）
- Menger, K. (1927). Zur allgemeinen Kurventheorie. *Fundamenta Mathematicae, 10*, 96–115. [https://doi.org/10.4064/fm-10-1-96-115](https://doi.org/10.4064/fm-10-1-96-115)。引用範圍：不相鄰兩點之間，分離所需的最少節點數等於內部不交路徑的最大數目。
- Minsky, H. P. (1992). *The financial instability hypothesis* (Working Paper No. 74). Jerome Levy Economics Institute. [官方 PDF](https://www.levyinstitute.org/pubs/wp74.pdf)。引用範圍：避險、投機與龐氏融資 <!-- term:PonziFinancing -->的定義（pp. 6–7）。
- OpenAI, & AMD. (2025, October 6). *AMD and OpenAI announce strategic partnership to deploy 6 gigawatts of AMD GPUs*. [官方網頁](https://openai.com/index/openai-amd-strategic-partnership/)
- OpenAI, & NVIDIA. (2025, September 22). *OpenAI and NVIDIA announce strategic partnership to deploy 10 gigawatts of NVIDIA systems*. [官方網頁](https://openai.com/index/openai-nvidia-systems-partnership/)。意向書，非已完成的投資。
- Schmitt, C. (2005). *Political theology: Four chapters on the concept of sovereignty* (G. Schwab, Trans.). University of Chicago Press. ISBN 9780226738895。德文初版 1922 年，Duncker & Humblot。文中以（Schmitt, 1922/2005）標示。
- Stone v. Ritter, 911 A.2d 362 (Del. 2006). （本次未取得法院官方線上來源；判決文本轉載：[OpenCasebook](https://opencasebook.org/documents/6083/)）
- U.S. House Committee on Transportation and Infrastructure. (2020). *Final committee report: The design, development & certification of the Boeing 737 MAX*. [官方報告 PDF](https://democrats-transportation.house.gov/imo/media/doc/2020.09.15%20FINAL%20737%20MAX%20Report%20for%20Public%20Release.pdf)。引用範圍：競爭壓力與改良既有機型的決定、發動機位置與 MCAS 的由來（pp. 38–43）、合成空速指示的提議（p. 18）、MCAS 依賴單一攻角感測器、西南航空合約條款（pp. 139, 148）、授權代表調查、合成空速方案（pp. 170–173）。
- U.S. Securities and Exchange Commission. (2002, September 24). *Dynegy settles securities fraud charges involving SPEs, round-trip energy trades* (Press Release 2002-140). [官方網頁](https://www.sec.gov/news/press/2002-140.htm)
- U.S. Securities and Exchange Commission. (2003). *The SEC, New York Attorney General's Office, NASD and the New York Stock Exchange permanently bar Jack Grubman and require $15 million payment* (Press Release 2003-55). [官方網頁](https://www.sec.gov/news/press/2003-55.htm)
- U.S. Securities and Exchange Commission. (2004). *SEC charges Qwest Communications International Inc. with multi-faceted accounting and financial reporting fraud* (Press Release 2004-148). [官方網頁](https://www.sec.gov/news/press/2004-148.htm)
- U.S. Securities and Exchange Commission. (2005). *Thomas J. Casey, Dan J. Cohrs, and Joseph P. Perrone* (Litigation Release No. 19179). [官方網頁](https://www.sec.gov/litigation/litreleases/lr19179.htm)。Global Crossing 揭露缺失的處分。
- U.S. Securities and Exchange Commission. (2022a). *SEC adopts compensation recovery listing standards and disclosure rules* (Press Release 2022-192). [官方網頁](https://www.sec.gov/newsroom/press-releases/2022-192)。文中簡稱 SEC, 2022a。
- U.S. Securities and Exchange Commission. (2022b). *Boeing to pay $200 million to settle SEC charges that it misled investors about the 737 MAX* (Press Release 2022-170). [官方網頁](https://www.sec.gov/newsroom/press-releases/2022-170)。文中簡稱 SEC, 2022b。
- U.S. Securities and Exchange Commission. (2022c). *SEC adopts amendments to modernize Rule 10b5-1 insider trading plans and related disclosures* (Press Release 2022-222). [官方網頁](https://www.sec.gov/newsroom/press-releases/2022-222)。文中簡稱 SEC, 2022c。