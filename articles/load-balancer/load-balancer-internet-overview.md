<properties 
   pageTitle ="網際網路面向的負載平衡器概觀 |Microsoft Azure 「
   描述 ="網際網路面向的負載平衡器和它的功能。 Azure 負載平衡器的運作方式使用的虛擬機器和雲端服務。 」
   服務 = 「 負載平衡器 」
   documentationCenter ="na"
   作者 ="joaoma"
   manager="adinah"
   編輯器 ="tysonn"/ >
<tags 
   ms.service= 」 負載平衡器 」
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload= 「 基礎結構服務 」
   ms.date="10/16/2015 」
   ms.author="joaoma"/ >


# 多個虛擬機器或服務之間的網際網路面向負載平衡器

端點的用法之一是流量的將特定類型的多個虛擬機器或服務之間的 Azure 負載平衡器的組態。 例如，您可以將 Web 要求的流量負載分散在多個 Web 伺服器或 Web 角色。

Azure 負載平衡器會從虛擬機器，將連入流量的 IP 位址和連接埠號碼公用對應至的私人 IP 位址和連接埠號碼的回應流量的虛擬機器，反之亦然。
>[AZURE.NOTE] Azure 負載平衡器會提供使用預設設定的多個虛擬機器執行個體的雜湊散發網路流量 (詳細資訊中的雜湊散發 [負載平衡器功能](load-balancer-overview.md#load-balancer-features) 。 如果您要尋找工作階段親和性，請參閱 [負載平衡器分配模式](load-balancer-distribution-mode.md)。

對於包含 Web 角色或背景工作角色執行個體的雲端服務，您可以在服務定義 (.csdef) 中定義公用端點。

Servicedefinition.csdef 檔案將包含端點組態，而當一個 Web 或背景工作角色部署有多個角色執行個體時，將會為它設定負載平衡器。 將執行個體加入至雲端部署的方法就是變更服務組態檔 (.csfg) 上的執行個體計數。

下圖顯示在三部虛擬機器中共用，且公用和私人 TCP 連接埠均為 443 的已加密 Web 流量負載平衡端點。 這三部虛擬機器均位在負載平衡集合中。


![公用負載平衡器範例](./media/load-balancer-internet-overview/IC727496.png))



當網際網路用戶端傳送網頁要求至雲端服務的公用 IP 位址與 TCP 連接埠 443 時，Azure 負載平衡器會對負載平衡集合中，介於這三部虛擬機器之間的要求執行以雜湊為基礎的負載平衡。 您可以取得有關在負載平衡器演算法的詳細資訊 [負載平衡器概觀頁面](load-balancer-overview.md#load-balancer-features)。


## 後續步驟

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始設定網際網路面向的負載平衡器](load-balancer-internet-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)








