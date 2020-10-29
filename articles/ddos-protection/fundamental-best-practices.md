---
title: Azure DDoS Protection alapvető ajánlott eljárások
description: A DDoS Protection használatával megismerheti a legjobb biztonsági eljárásokat.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 594a7e2a6977cc2a7052a15e1a007c68c08da259
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905313"
---
# <a name="fundamental-best-practices"></a>Alapvető ajánlott eljárások

A következő fejezetekben részletes útmutatást talál a DDoS-rugalmas szolgáltatások Azure-beli létrehozásához.

## <a name="design-for-security"></a>Biztonságot szem előtt tartó tervezés

Győződjön meg arról, hogy a biztonság az alkalmazás teljes életciklusa alatt, a tervezéstől és a megvalósítástól az üzembe helyezésig és a műveletekig prioritás. Az alkalmazások olyan hibával rendelkezhetnek, amely lehetővé teszi, hogy viszonylag alacsony mennyiségű kérést használjon az erőforrások meghívására, ami a szolgáltatás kimaradását eredményezi. 

A Microsoft Azureon futó szolgáltatások védelmének elősegítése érdekében érdemes megismernie az alkalmazás architektúráját, és a [szoftver minőségének öt pillérére](/azure/architecture/guide/pillars)kell összpontosítania.
Ismernie kell a jellemző adatforgalmi köteteket, a kapcsolati modellt az alkalmazás és más alkalmazások között, valamint a nyilvános interneten elérhető szolgáltatási végpontokat.

Fontos, hogy az alkalmazások elég rugalmasak legyenek ahhoz, hogy az alkalmazásra irányuló szolgáltatásmegtagadás ne legyen a legfontosabb. A biztonság és az adatvédelem az Azure platformra épül, a [biztonsági fejlesztési életciklussal (SDL)](https://www.microsoft.com/sdl/default.aspx)kezdve. Az SDL minden fejlesztési fázisban kezeli a biztonságot, és gondoskodik arról, hogy az Azure-t folyamatosan frissítsék, hogy még biztonságosabb legyen.

## <a name="design-for-scalability"></a>Skálázhatóság kialakítása

A méretezhetőség azt szemlélteti, hogy a rendszer milyen jól tudja kezelni a megnövekedett terhelést. Megtervezheti, hogy az alkalmazások [horizontálisan méretezhetők](/azure/architecture/guide/design-principles/scale-out) legyenek, hogy megfeleljenek egy felerősített terhelés igényének, különösen a DDOS-támadások esetén. Ha az alkalmazása egy szolgáltatás egyetlen példányán múlik, akkor az egyetlen meghibásodási pontot hoz létre. A több példány kiépítés révén a rendszerek rugalmasabbak és méretezhetők.

[Azure app Service](/azure/app-service/app-service-value-prop-what-is)esetén válasszon ki egy olyan [app Service tervet](/azure/app-service/overview-hosting-plans) , amely több példányt is kínál. Az Azure Cloud Services esetében konfigurálja az egyes szerepköröket [több példány](/azure/cloud-services/cloud-services-choose-me)használatára. Az [Azure Virtual Machines](../virtual-machines/index.yml)esetén győződjön meg arról, hogy a virtuális gép (VM) architektúrája több virtuális gépet tartalmaz, és hogy minden virtuális gép egy [rendelkezésre állási csoportba](../virtual-machines/windows/tutorial-availability-sets.md)tartozik. Javasoljuk, hogy használjon [virtuálisgép-méretezési csoportokat](../virtual-machine-scale-sets/overview.md) az automatikus skálázási képességekhez.

## <a name="defense-in-depth"></a>Mélységi védelem

A védelem részletesen a különböző védelmi stratégiák használatával kezelhető a kockázatkezelés. Az alkalmazásokban a biztonsági védelem egyre csökkenti a sikeres támadás lehetőségét. Javasoljuk, hogy az Azure platform beépített képességeinek használatával hozzon létre biztonságos terveket az alkalmazásaihoz.

Például a támadás kockázata az alkalmazás méretével ( *felületével* ) nő. A felszín területét egy jóváhagyási lista használatával csökkentheti a kihelyezett IP-címtartomány és a terheléselosztó által nem szükséges portok figyelése ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) és az [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)). A [hálózati biztonsági csoportok (NSG)](/azure/virtual-network/security-overview) egy másik módszer a támadási felület csökkentésére.
A [szolgáltatási címkék](/azure/virtual-network/security-overview#service-tags) és az [alkalmazás-biztonsági csoportok](/azure/virtual-network/security-overview#application-security-groups) használatával csökkentheti a biztonsági szabályok létrehozásának összetettségét, és konfigurálhatja a hálózati biztonságot az alkalmazások struktúrájának természetes kiterjesztéseként.

Amikor csak lehetséges, üzembe kell helyeznie az Azure-szolgáltatásokat egy [virtuális hálózaton](/azure/virtual-network/virtual-networks-overview) . Ez a gyakorlat lehetővé teszi a szolgáltatási erőforrások számára, hogy magánhálózati IP-címeken keresztül kommunikáljanak egymással. A virtuális hálózatról származó Azure-szolgáltatási forgalom alapértelmezés szerint a nyilvános IP-címeket használja forrás IP-címként. A [szolgáltatási végpontok](/azure/virtual-network/virtual-network-service-endpoints-overview) használatával a szolgáltatás forgalmát úgy fogja váltani, hogy a virtuális hálózati magánhálózati címeket használják forrás IP-címként, amikor egy virtuális hálózatról érik el az Azure-szolgáltatást.

Gyakran tekintjük meg az ügyfelek helyszíni erőforrásait az Azure-beli erőforrásaikkal együtt. Ha helyszíni környezetet csatlakoztat az Azure-hoz, javasoljuk, hogy csökkentse a helyszíni erőforrások a nyilvános interneten való használatát. Az Azure-ban a jól ismert nyilvános entitásokat az Azure-ban üzembe helyezheti az Azure Scale és Advanced DDoS Protection képességeivel. Mivel ezek a nyilvánosan elérhető entitások gyakran a DDoS-támadások célpontja, az Azure-ban való üzembe helyezésük csökkenti a helyszíni erőforrásokra gyakorolt hatást.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozhat létre DDoS Protection-tervet](manage-ddos-protection.md).