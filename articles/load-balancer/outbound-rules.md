---
title: Kimenő szabályok Azure Load Balancer
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a kimenő szabályokat az internetes forgalom Azure Load Balancer-vel való megadásának szabályozására.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002640"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Kimenő szabályok Azure Load Balancer

A kimenő szabályok lehetővé teszik a nyilvános standard Load Balancer kimenő SNAT (forrásoldali hálózati címfordítás) konfigurálását. Ez a konfiguráció lehetővé teszi, hogy a terheléselosztó nyilvános IP-címeit használja proxyként.

Ez a konfiguráció a következőket teszi lehetővé:

* IP-cím maszkolása
* Egyszerűsítse az engedélyezési listát.
* Csökkenti a nyilvános IP-erőforrások számát az üzembe helyezéshez.

A kimenő szabályok teljes mértékben deklaratív vezérléssel rendelkeznek a kimenő internetkapcsolattal szemben. A kimenő szabályok lehetővé teszik az adott igényeknek megfelelő méretezést és finomhangolást. 

A kimenő szabályok csak akkor lesznek követve, ha a háttérbeli virtuális gép nem rendelkezik példány szintű nyilvános IP-címmel (ILPIP).

![Load Balancer kimenő szabályok](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

A kimenő szabályok használatával explicit módon definiálhatja a kimenő **SNAT** viselkedését.

A kimenő szabályok segítségével a következőket vezérelheti:

* **Mely virtuális gépek fordíthatók le a nyilvános IP-címekre.**
     * Két szabály volt a háttér-készlet, amely az A és B IP-címet használja, a B háttér-készlet pedig a C és A D IP-címet használja.
* **A kimenő SNAT portok megadják.**
     * A "B" háttér-készlet az egyetlen olyan készlet, amely kimenő kapcsolatokat biztosít, és az összes SNAT-portot a "B" háttér-készletbe, a "A" háttérbeli
* **A kimenő fordítását biztosító protokollok.**
     * A B háttér-készletnek a kimenő UDP-portokra van szüksége. A háttérbeli készlethez TCP szükséges. TCP-portokat adjon a és az UDP-portoknak a B értékre.
* **A kimenő kapcsolat üresjárati időkorlátja (4-120 perc) milyen időtartamra van használatban.**
     * Ha hosszú ideig futó kapcsolatok vannak a Keepalives-mel, a hosszú ideig futó kapcsolatok esetében foglaljon le üresjárati portokat akár 120 percre. Az elavult kapcsolatok elhagyása és a portok 4 percen belüli felszabadítása friss kapcsolatok esetén 
* **Azt jelzi, hogy a TCP alaphelyzetbe állítása üresjárati időkorláton történjen.**
     * az üresjárati kapcsolatok időtúllépése esetén a rendszer az ügyfél és a kiszolgáló számára is elküld egy TCP-t, hogy tudja, hogy a folyamat el lett hagyva?

## <a name="outbound-rule-definition"></a>Kimenő szabály definíciója

A kimenő szabályok ugyanazt a megszokott szintaxist követik, mint a terheléselosztás és a bejövő NAT- **szabályok: előtér**-  +  **Paraméterek**  +  **háttér-készlete**. 

Egy kimenő szabály konfigurálja a kimenő NAT-t a _háttér-készlet által azonosított összes virtuális gép_ számára, hogy a rendszer lefordítsa a _felületet_.  

A _Paraméterek_ további részletes szabályozást biztosítanak a kimenő NAT-algoritmushoz képest.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Kimenő NAT méretezése több IP-címmel

A frontend által biztosított további IP-címek további 64 000 ideiglenes portokat biztosítanak a terheléselosztó számára SNAT-portokként való használathoz. 

Több IP-cím használata a nagy léptékű forgatókönyvek megtervezéséhez. A kimenő szabályok használatával csökkentheti a [SNAT-kimerültséget](troubleshoot-outbound-connection.md#snatexhaust). 

A [nyilvános IP-előtagot](https://aka.ms/lbpublicipprefix) közvetlenül is használhatja egy kimenő szabállyal. 

A nyilvános IP-előtag növeli az üzemelő példány skálázását. Az előtag hozzáadható az Azure-erőforrásokból származó folyamatok engedélyezési listájához. A Load balancerben konfigurálhatja a előtérbeli IP-konfigurációt a nyilvános IP-cím előtagjaként való hivatkozáshoz.  

A terheléselosztó felügyeli a nyilvános IP-előtagot. A kimenő kapcsolatok esetében a kimeneti szabály automatikusan a nyilvános IP-előtagban található összes nyilvános IP-címet fogja használni. 

A nyilvános IP-előtagon belüli összes IP-cím egy további 64 000 ideiglenes portot biztosít IP-címként a terheléselosztó számára SNAT-portokként való használathoz.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Kimenő forgalom üresjárati időkorlátja és a TCP-visszaállítás

A kimenő szabályok egy konfigurációs paramétert biztosítanak a kimenő folyamat üresjárati időkorlátjának szabályozására, és az alkalmazás igényeinek megfelelően illeszkednek. A kimenő üresjárati időtúllépések alapértelmezett értéke 4 perc. További információ: az [üresjárati időtúllépések konfigurálása](load-balancer-tcp-idle-timeout.md). 

A terheléselosztó alapértelmezett viselkedése a folyamat csendes eldobása, ha elérte a kimenő üresjárati időkorlátot. A `enableTCPReset` paraméter lehetővé teszi a kiszámítható alkalmazások viselkedését és vezérlését. A paraméter azt határozza meg, hogy a kimenő Üresjárati időkorlát időtúllépése esetén a kétirányú TCP alaphelyzetbe állítás (TCP első) legyen-e. 

Tekintse át az [Üresjárat időkorlátjának TCP-visszaállítását](https://aka.ms/lbtcpreset) , beleértve a régió rendelkezésre állását.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>A kimenő kapcsolatok biztonságossá tétele és szabályozása explicit módon

Terheléselosztási szabályok biztosítják a kimenő NAT automatikus programozását. Bizonyos forgatókönyvek esetében a kimenő NAT automatikus programozásának letiltására van szükség a terheléselosztási szabály alapján. A szabály használatával történő letiltás lehetővé teszi a viselkedés szabályozását vagy pontosítását.  

Ezt a paramétert kétféleképpen is használhatja:

1. A kimenő SNAT bejövő IP-címének megakadályozása. Tiltsa le a kimenő SNAT a terheléselosztási szabályban.
  
2. A bejövő és kimenő IP-címek kimenő **SNAT** paramétereinek hangolása egyidejűleg. Az automatikus kimenő NAT-t le kell tiltani ahhoz, hogy a kimenő szabályok szabályozni tudják a vezérlést. Ha módosítani szeretné egy SNAT-port lefoglalását is, akkor a `disableOutboundSnat` paramétert igaz értékre kell állítani. 

A kimenő szabályok konfigurálásának művelete sikertelen lesz, ha megpróbál újradefiniálni egy bejövő IP-címet.  Először tiltsa le a terheléselosztási szabály kimenő NAT-át.

>[!IMPORTANT]
> Ha a paraméter értéke TRUE (igaz), a virtuális gép nem rendelkezik kimenő kapcsolattal, és nem rendelkezik kimenő kapcsolattal.  A virtuális gép vagy az alkalmazás néhány művelete attól függ, hogy a kimenő kapcsolat elérhető-e. Győződjön meg arról, hogy tisztában van a forgatókönyv függőségeivel, és befolyásolta ennek a változásnak a hatását.

Esetenként nem kívánatos, hogy egy virtuális gép kimenő folyamatot hozzon létre. Előfordulhat, hogy egy olyan követelményt kell kezelnie, hogy mely célhelyek kapják meg a kimenő folyamatokat, vagy hogy mely célhelyek kezdenek bejövő forgalmat. [Hálózati biztonsági csoportok](../virtual-network/security-overview.md) használatával kezelheti a virtuális gép által elnyúló célhelyeket. A NSG használatával felügyelheti, hogy mely nyilvános célhelyek indítják el a bejövő folyamatokat.

Ha NSG alkalmaz egy elosztott terhelésű virtuális gépre, ügyeljen a [szolgáltatás-címkékre](../virtual-network/security-overview.md#service-tags) és az [alapértelmezett biztonsági szabályokra](../virtual-network/security-overview.md#default-security-rules). 

Győződjön meg arról, hogy a virtuális gép Azure Load Balancertól származó állapot-mintavételi kérelmeket tud fogadni.

Ha egy NSG letiltja az állapot-mintavételi kérelmeket a AZURE_LOADBALANCER alapértelmezett címkétől, a virtuális gép állapotának mintavétele meghiúsul, és a virtuális gép nem érhető el. A terheléselosztó leállítja az új folyamatok küldését az adott virtuális gépre.

## <a name="limitations"></a>Korlátozások

- A felhasználható ideiglenes portok maximális száma előtér-IP-címenként 64 000.
- A konfigurálható kimenő Üresjárati időkorlát (4 – 120 perc) tartománya (240 – 7200 másodperc).
- A Load Balancer nem támogatja az ICMP protokollt a kimenő NAT-hoz.
- A kimenő szabályok csak a hálózati adapter elsődleges IP-konfigurációjához alkalmazhatók.  Nem hozható létre Kimenő szabály a virtuális gép vagy NVA másodlagos IP-címéhez. Több hálózati adapter is támogatott.
- A kimenő kapcsolatok esetében a **rendelkezésre állási csoporton** belüli összes virtuális gépet hozzá kell adni a háttérbeli készlethez. 
- A **virtuálisgép-méretezési csoporton** belüli összes virtuális gépet a kimenő kapcsolathoz hozzá kell adni a háttérbeli készlethez.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure standard Load Balancer](load-balancer-overview.md)
- Tekintse meg a [Azure Load Balancerekkel kapcsolatos gyakori kérdéseket](load-balancer-faqs.md)

