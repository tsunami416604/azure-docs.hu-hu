---
title: Azure Security Center a gépekre vonatkozó ajánlásokat & alkalmazások
description: Azure Security Center biztonsági javaslatai segítenek a virtuális gépek, a számítógépek, a webalkalmazások és a App Service környezetek védelmében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2019
ms.author: memildin
ms.openlocfilehash: c0cf5951daf004a0c805b688f08d1ccfa4679615
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74782163"
---
# <a name="compute-and-app-recommendations---reference-guide"></a>Számítási és alkalmazási javaslatok – útmutató

Ez a cikk a következő erőforrástípusok esetén megjelenő ajánlások teljes listáját tartalmazza Azure Security Centerban:

* Virtuális gépek és számítógépek
* VM Scale Sets
* Cloud Services
* App Services
* Containers
* Számítási erőforrások

A [következő témakörből megtudhatja](security-center-virtual-machine-protection.md), hogyan keresheti meg ezeket, és hogyan oldhatja meg őket.

## Számítási és alkalmazási javaslatok<a name="compute-and-app-recs"></a>
|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|App Service|20|A webalkalmazás csak HTTPS protokollon keresztül érhető el|A webalkalmazások hozzáférésének korlátozása csak HTTPS protokollon keresztül.|
|App Service|20|függvényalkalmazás csak HTTPS-kapcsolaton keresztül érhető el|Csak HTTPS-kapcsolaton keresztül korlátozza a függvények elérését.|
|App Service|5|A App Services diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|App Service|10|A távoli hibakeresést ki kell kapcsolni a webalkalmazáshoz|Ha már nincs szüksége rá, kapcsolja ki a webes alkalmazások hibakeresését. A távoli hibakereséshez a bejövő portokat függvényalkalmazás kell megnyitni.|
|App Service|10|A távoli hibakeresést ki kell kapcsolni a Function alkalmazáshoz|Ha már nincs szüksége a használatra, kapcsolja ki a hibakeresést függvényalkalmazás. A távoli hibakereséshez a bejövő portokat függvényalkalmazás kell megnyitni.|
|App Service|10|Ne engedélyezze az összes (*) erőforrást az alkalmazás eléréséhez| Ne engedélyezze a WEBSITE_LOAD_CERTIFICATES paramétert "" értékre. A paraméter "" értékre állítása azt jelenti, hogy az összes tanúsítvány betöltődik a webalkalmazások személyes tanúsítványtárolóba. Ez a legalacsonyabb jogosultsági szinttel való visszaéléshez vezethet, mivel nem valószínű, hogy a helynek a futtatókörnyezet összes tanúsítványához hozzá kell férnie.|
|App Service|20|A CORS nem teszi lehetővé minden erőforrás számára a webalkalmazások elérését|Csak a szükséges tartományokat engedélyezze a webalkalmazással való kommunikációhoz. A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára a webalkalmazás elérését.|
|App Service|20|A CORS nem teszi lehetővé minden erőforrás számára a függvényalkalmazás elérését| Csak a szükséges tartományokat engedélyezze a Function alkalmazással való kommunikációhoz. A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé az összes tartomány számára a Function alkalmazás elérését.|
|Számítási erőforrások (batch)|1|A metrika riasztási szabályait a Batch-fiókokon kell konfigurálni|Metrika riasztási szabályainak konfigurálása a Batch-fiókon, valamint a metrikák készletének törlése a teljes események és a készlet törlése indítási események|
|Számítási erőforrások (Service Fabric)|10|Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez|Az ügyfél-hitelesítés csak a Service Fabric Azure Active Directoryon keresztül hajtható végre.|
|Számítási erőforrások (Automation-fiók)|5|Az Automation-fiók változóit titkosítani kell|Az Automation-fiókok változó eszközei titkosításának engedélyezése bizalmas adatok tárolására.|
|Számítási erőforrások (keresés)|5|Diagnosztikai naplók engedélyezésének naplózása a keresési szolgáltatásokhoz|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (Service Bus)|5|A Service Bus diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (stream Analytics)|5|A Azure Stream Analytics diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (batch)|5|Diagnosztikai naplók engedélyezése a Batch-fiókokban|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (Event hub)|5|Az Event hub diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (logikai alkalmazások)|5|Diagnosztikai naplók engedélyezése Logic Apps|Engedélyezheti a naplókat, és akár egy évig megtarthatja őket. Ez lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidens bekövetkeztekor vagy a hálózat biztonsága esetén vizsgálat céljára történjen. |
|Számítási erőforrások (Service Fabric)|15|Állítsa a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre Service Fabric|A Service Fabric három védelmi szintet biztosít (nincs, aláírás és EncryptAndSign) a csomópontok közötti kommunikációhoz az elsődleges fürt tanúsítványának használatával. Állítsa be a védelmi szintet annak biztosítására, hogy minden csomópont-csomópont típusú üzenet titkosítva legyen, és digitálisan alá legyen írva. |
|Számítási erőforrások (Service Bus)|1|Az összes engedélyezési szabály eltávolítása Service Bus névtérből származó RootManageSharedAccessKey kivételével |Service Bus-ügyfelek nem használhatnak névtér szintű hozzáférési szabályzatot, amely hozzáférést biztosít a névtérben lévő összes várólistához és témakörhöz. A legalacsonyabb jogosultsági szintű biztonsági modellel való összehangoláshoz hozzáférési házirendeket kell létrehoznia az entitások szintjén a várólistákhoz és a témakörökhöz, hogy csak az adott entitáshoz lehessen hozzáférést biztosítani.|
|Számítási erőforrások (Event hub)|1|A RootManageSharedAccessKey kivételével az összes engedélyezési szabályt el kell távolítani az Event hub-névtérből|Az Event hub-ügyfelek nem használhatnak olyan névtér szintű hozzáférési szabályzatot, amely hozzáférést biztosít a névtérben lévő összes várólistához és témakörhöz. A legalacsonyabb jogosultsági szintű biztonsági modellel való összehangoláshoz hozzáférési házirendeket kell létrehoznia az entitások szintjén a várólistákhoz és a témakörökhöz, hogy csak az adott entitáshoz lehessen hozzáférést biztosítani.|
|Számítási erőforrások (Event hub)|5|Az Event hub-entitás engedélyezési szabályait definiálni kell|Az Event hub-entitás engedélyezési szabályainak naplózása a legkevésbé privilegizált hozzáférés biztosításához.|
|Gép|50|Figyelési ügynök telepítése a gépeken|Telepítse a figyelési ügynököt az adatgyűjtés engedélyezéséhez, a frissítések vizsgálatához, az alapkonfiguráció vizsgálatához és az Endpoint Protection szolgáltatáshoz az egyes gépeken.|
|Gép|50|Automatikus kiépítés és adatgyűjtés engedélyezése az előfizetésekhez |Az előfizetésekben lévő gépek automatikus kiépítési és adatgyűjtési szolgáltatásának engedélyezése az adatgyűjtésre, a frissítések vizsgálatára, az alapkonfiguráció vizsgálatára és az Endpoint Protection szolgáltatásra az előfizetésekhez hozzáadott összes gépen.|
|Gép|40|A figyelési ügynök állapotával kapcsolatos problémák megoldása a gépeken|A teljes Security Center védelem érdekében a hibaelhárítási útmutató utasításait követve oldja meg a figyelési ügynökkel kapcsolatos problémákat a számítógépeken.| 
|Gép|40|Az Endpoint Protection állapotával kapcsolatos problémák megoldása a gépeken|A teljes Security Center védelem érdekében a hibaelhárítási útmutató utasításait követve oldja meg a figyelési ügynökkel kapcsolatos problémákat a számítógépeken.|
|Gép|40|A számítógépek hiányzó vizsgálati adataival kapcsolatos hibák megoldása|A virtuális gépeken és számítógépeken a hiányzó vizsgálati adatvizsgálatok hibáinak megoldása. A számítógépeken a vizsgálati eredmények hiányzó biztonsági vizsgálatokat eredményeznek, például a frissítések vizsgálatát, az alapkonfiguráció vizsgálatát és az Endpoint Protection-megoldások hiányzó vizsgálatát.|
|Gép|40|A számítógépekre telepíteni kell a rendszerfrissítéseket|A Windows és a Linux rendszerű virtuális gépek és számítógépek biztonságossá tételéhez telepítse a hiányzó rendszerbiztonsági és kritikus frissítéseket
|Gép|15|Webalkalmazási tűzfal hozzáadása| Helyezzen üzembe egy webalkalmazási tűzfal (WAF) megoldást a webalkalmazások védelmére. |
|Gép|40|A Cloud Service-szerepkörök operációsrendszer-verziójának frissítése|Frissítse a Cloud Service szerepköreihez tartozó operációs rendszer (OS) verzióját az operációsrendszer-család számára elérhető legújabb verzióra.|
|Gép|35|A gépek biztonsági beállításainak sebezhetőségeit szervizelni kell|Javítsa ki a számítógépeken a biztonsági beállítások sebezhetőségeit a támadások elleni védelem érdekében.|
|Gép|35|Biztonsági rések szervizelése a tárolók biztonsági beállításaiban|Javítsa ki a biztonsági beállításokat a Docker által telepített számítógépeken a támadások elleni védelem érdekében.|
|Gép|25|Adaptív alkalmazások vezérlőinek engedélyezése|Az alkalmazások vezérlésének engedélyezésével szabályozhatja, hogy mely alkalmazások futhatnak az Azure-ban található virtuális gépeken. Ez segít megerősíteni a virtuális gépeket a kártevők ellen. Security Center a gépi tanulás segítségével elemzi az egyes virtuális gépeken futó alkalmazásokat, és segít az engedélyezési szabályok alkalmazásában az intelligenciával. Ez a funkció leegyszerűsíti az alkalmazások engedélyezési szabályainak konfigurálását és karbantartását.|
|Gép|20|Endpoint Protection-megoldás telepítése a számítógépekre|Telepítsen egy Endpoint Protection-megoldást a virtuális gépekre a fenyegetések és a biztonsági rések elleni védelem érdekében.|
|Gép|20|A rendszerfrissítések alkalmazásához indítsa újra a gépeket|Indítsa újra a gépeket a rendszerfrissítések alkalmazásához, és a számítógép védelmét a biztonsági rések alapján.|
|Gép|15|A lemezes titkosítást a virtuális gépeken kell alkalmazni|A Windows-és Linux-alapú virtuális gépekhez Azure Disk Encryption egyaránt titkosíthatja a virtuális gépek lemezeit. Azure Disk Encryption (ADE) kihasználja a Windows iparági szabvány BitLocker szolgáltatását és a Linux DM-Crypt szolgáltatását, hogy az operációs rendszer és az adatlemez-titkosítás biztosítson az adatai védelméhez és védelméhez, valamint a szervezeti biztonság és megfelelőség biztosításához az ügyfél Azure Key vaultban vállalt kötelezettségek. Ha a megfelelőségi és biztonsági követelmény megköveteli, hogy a titkosítási kulcsok használatával a végpontok között titkosítsa az adatait, beleértve az időszakos (helyileg csatolt ideiglenes) lemez titkosítását, használja az Azure Disk encryptiont. Másik lehetőségként alapértelmezés szerint a Managed Disks titkosítva maradnak az Azure Storage Service Encryption használatával, ahol a titkosítási kulcsok a Microsoft által felügyelt kulcsok az Azure-ban. Ha ez megfelel a megfelelőségi és biztonsági követelményeknek, kihasználhatja az alapértelmezett felügyelt lemezes titkosítást, hogy megfeleljen az igényeinek.|
|Gép|30|Sebezhetőség-felmérési megoldás telepítése a virtuális gépeken|Sebezhetőség-felmérési megoldás telepítése a virtuális gépeken|
|Gép|15|Webalkalmazási tűzfal hozzáadása| Helyezzen üzembe egy webalkalmazási tűzfal (WAF) megoldást a webalkalmazások védelmére. |
|Gép|30|A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni|Azok a virtuális gépek, amelyeken a sebezhetőségi felmérés harmadik féltől származó megoldást helyeznek üzembe, folyamatosan értékelik az alkalmazások és az operációs rendszer biztonsági réseit. Ha ilyen biztonsági réseket talál, ezek a javaslat részeként további információkhoz is elérhetők.|
|Gép|30|Sebezhetőség-felmérési megoldás telepítése a virtuális gépeken|Sebezhetőség-felmérési megoldás telepítése a virtuális gépeken|
|Gép|1|A virtuális gépeket át kell telepíteni az új AzureRM-erőforrásokra|A virtuális gépek Azure Resource Manager használatával olyan biztonsági fejlesztéseket biztosíthat, mint például a következők: erősebb hozzáférés-vezérlés (RBAC), jobb auditálás, erőforrás-kezelő alapú üzembe helyezés és irányítás, felügyelt identitásokhoz való hozzáférés, a Key Vault for Secrets elérése Az Azure AD-alapú hitelesítés és a címkék és erőforráscsoportok támogatása a biztonsági felügyelet megkönnyítésére. |
|Gép|30|A biztonsági réseket a sebezhetőség-felmérési megoldásnak kell szervizelni|Azok a virtuális gépek, amelyeken a sebezhetőségi felmérés harmadik féltől származó megoldást helyeznek üzembe, folyamatosan értékelik az alkalmazások és az operációs rendszer biztonsági réseit. Ha ilyen biztonsági réseket talál, ezek a javaslat részeként további információkhoz is elérhetők.|
|Virtuálisgép-méretezési csoport |4|A Virtual Machine Scale Sets diagnosztikai naplóit engedélyezni kell|Engedélyezheti a naplókat, és akár egy évig is megtarthatja őket. Ez lehetővé teszi, hogy vizsgálati célokra újra létrehozza a tevékenységek nyomvonalait. Ez akkor hasznos, ha biztonsági incidens következik be, vagy a hálózat biztonsága sérül.|
|Virtuálisgép-méretezési csoport|35|A virtuális gépek méretezési csoportjainak biztonsági beállításaiban található biztonsági réseket szervizelni kell|Javítsa a biztonsági beállításokat a virtuálisgép-méretezési csoportokban a támadások elleni védelem érdekében. |
|Virtuálisgép-méretezési csoport|5|Az Endpoint Protection állapotával kapcsolatos hibák elhárítása virtuálisgép-méretezési csoportokban|Javítsa ki az Endpoint Protection-állapottal kapcsolatos hibákat a virtuálisgép-méretezési csoportokban a fenyegetések és a biztonsági rések elleni védelem érdekében. |
|Virtuálisgép-méretezési csoport|10|Az Endpoint Protection szolgáltatást virtuális gépekre kell telepíteni|Telepítsen egy Endpoint Protection-megoldást a virtuálisgép-méretezési csoportokra a fenyegetések és a biztonsági rések elleni védelem érdekében. |
|Virtuálisgép-méretezési csoport|40|A virtuálisgép-méretezési csoportokra vonatkozó rendszerfrissítéseket telepíteni kell|Telepítse a hiányzó rendszerbiztonsági és kritikus frissítéseket a Windows és a Linux rendszerű virtuálisgép-méretezési csoportok biztonságossá tételéhez. |
|


## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a más Azure-erőforrásokra vonatkozó javaslatokról, tekintse meg a következőket:

* [Identitás és hozzáférés figyelése az Azure Security Centerben](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás védelme Azure Security Center](security-center-sql-service-recommendations.md)
