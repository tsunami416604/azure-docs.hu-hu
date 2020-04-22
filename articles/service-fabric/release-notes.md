---
title: Az Azure Service Fabric kiadásai
description: Kibocsátási megjegyzések az Azure Service Fabric. A Service Fabric legújabb funkcióival és fejlesztéseivel kapcsolatos információkat tartalmazza.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 3e0f6c78b6e5dd066cbfbac6805bb3c42068e66a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729598"
---
# <a name="service-fabric-releases"></a>A Service Fabric kiadásai

| <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Hibaútmutatók</a> 
| <a href="https://github.com/Azure/service-fabric-issues" target="blank">Problémakövetési</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-support" target="blank">támogatási lehetőségek</a> 
| <a href="https://docs.microsoft.com/azure/service-fabric/service-fabric-versions" target="blank">– Támogatott verziók</a> 
| <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">kódminták</a>

Ez a cikk további információkat a Service Fabric futásidejű és SDK-k legújabb kiadásairól és frissítéseiről tartalmaz.

## <a name="whats-new-in-service-fabric"></a>A Service Fabric újdonságai

### <a name="service-fabric-71"></a>Szolgáltatás Fabric 7.1
A jelenlegi COVID-19 válság miatt, és figyelembe véve az ügyfeleink előtt álló kihívásokat, elérhetővé tesszük a 7.1-et, de nem frissítjük automatikusan a beállított fürtöket, hogy automatikus frissítéseket kapjanak. További értesítésig szüneteltetjük az automatikus frissítéseket annak érdekében, hogy az ügyfelek a számukra legmegfelelőbb esetben is alkalmazhassák a frissítéseket a váratlan zavarok elkerülése érdekében.

A 7.1-es cikkaz Azure [Portalon](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) vagy az [Azure Resource Manager központi telepítésén](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-version-azure#set-the-upgrade-mode-using-a-resource-manager-template)keresztül frissíthető.

Service Fabric fürtök automatikus frissítés engedélyezve lesz a 7.1-es frissítés automatikusan, amint folytatjuk a szabványos bevezetési eljárás. Adunk egy másik bejelentést, mielőtt a standard bevezetése kezdődik a [Service Fabric Tech közösségi oldalon](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
Mi is közzétett frissítéseket, hogy vége a támogatás időpontját a főbb kiadások kezdve 6,5-7,1 [itt](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-versions). 

## <a name="what-is-new-in-service-fabric-71"></a>Mi az új in-Service Fabric 7.1?
Izgatottan várjuk, hogy bejelentsük a Service Fabric következő kiadását. Ez a kiadás tele van kulcsfontosságú funkciókkal és fejlesztésekkel. A legfontosabb jellemzők közül néhány at az alábbiakban emeljük ki:
## <a name="key-announcements"></a>Legfontosabb közlemények
- A Service Fabric felügyelt identitásainak **általános elérhetősége** [ **a Service Fabric-alkalmazásokhoz**](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity)
- [**Ubuntu 1804 támogatása**](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-create-vnet-and-linux-cluster)
 - [**Előzetes verzió: VMSS Ephemeral OS lemez támogatása**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-azure-deployment-preparation#use-ephemeral-os-disks-for-virtual-machine-scale-sets)**: Az ideiglenes operációsrendszer-lemezek a helyi virtuális gépen létrehozott tárolók, és nem kerülnek mentésre a távoli Azure Storage-ba. Ezek minden Service Fabric-csomóponttípushoz (elsődleges és másodlagos) ajánlottak, mivel a hagyományos állandó operációsrendszer-lemezekhez, a rövid élettartamú operációsrendszer-lemezekhez képest:
      -  Az olvasási/írási késés csökkentése az operációs rendszer lemezére
      -  Gyorsabb alaphelyzetbe állítási/újraképzési csomópontkezelési műveletek engedélyezése
      -  Csökkentse a teljes költségeket (a lemezek ingyenesek, és nincsenek további tárolási költségek)
- A Service [**Fabric-alkalmazások szolgáltatásvégpont-tanúsítványainak bejelentésének**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-manifest-resources)támogatása tulajdonos név szerint.
- [**Az állapotalapú szolgáltatások állapotvizsgálatának támogatása:**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage)A tárolóba adtaalkalmazások liveness probe mechanizmusának támogatása. A Liveness Probe segít bejelenteni a tárolóba rendezett alkalmazás élőségét, és ha nem válaszolnak időben, az újraindítást eredményez. 
- [**Támogatja az initializer kódcsomagok**](https://docs.microsoft.com/azure/service-fabric/initializer-codepackages) [tárolók](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-containers-overview) és [vendég végrehajtható](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-guest-executables-introduction) alkalmazások támogatása. Ez lehetővé teszi a kódcsomagok (pl. tárolók) adott sorrendben történő végrehajtását a Service Package inicializálásához.
- **FabricObserver és ClusterObserver** állapot nélküli alkalmazások, amelyek rögzítik a Service Fabric telemetriai kapcsolatos különböző aspektusait egy SF-fürt. Mindkét alkalmazás készen áll a Windows éles fürtöküzembe helyezésére, hogy az ApplicationInsights, az EventSource és a LogAnalytics implementált támogatásával gazdag telemetriát rögzítsenek.
    - [**FabricObserver (FO) 2.0**](https://github.com/microsoft/service-fabric-observer)- fut az összes csomóponton, létrehoz egészségügyi események, telemetriai adatokat bocsát ki, amikor a felhasználó által konfigurált erőforrás-használati küszöbértékek elérésekor. Ez a kiadás számos fejlesztést tartalmaz a figyelés, az adatkezelés, az állapotesemény részletei, strukturált telemetriai.
     - [**ClusterObserver (CO) 1.1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) - fut egy csomóponton, rögzíti a fürtszintű egészségügyi telemetriai adatok. Ebben a kiadásban a ClusterObserver is figyeli a csomópont állapotát, és telemetriai adatokat bocsát ki, ha a csomópont a felhasználó által megadott időtartamnál hosszabb ideig van le)/letiltva/letiltva.

### <a name="improve-application-life-cycle-experience"></a>Az alkalmazások életciklusának élményének javítása

- **[Előzetes:Kérelem drain](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-advanced#avoid-connection-drops-during-planned-downtime-of-stateless-services)**: A tervezett szolgáltatás karbantartása, például a szolgáltatás frissítése vagy a csomópont kikapcsolása során szeretné engedélyezni, hogy a szolgáltatások szabályosan ürítsék ki a kapcsolatokat. Ez a szolgáltatás hozzáadja a példány közeli késleltetési időtartamát a szolgáltatás konfigurációjában. A tervezett műveletek során az SF eltávolítja a szolgáltatás címét a felderítésből, majd megvárja ezt az időtartamot, mielőtt leállítaná a szolgáltatást.
- **[Subcluster észlelés és kiegyensúlyozás automatikus észlelése és kiegyensúlyozása](https://docs.microsoft.com/azure/service-fabric/cluster-resource-manager-subclustering )**: Az alfürtözés akkor történik, ha a különböző elhelyezési korlátozásokkal rendelkező szolgáltatások közös [terhelési metrikával](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-metrics)rendelkeznek . Ha a különböző csomópontok terhelése jelentősen eltér, a Service Fabric-fürt erőforrás-kezelő úgy véli, hogy a fürt kiegyensúlyozatlan, még akkor is, ha az elhelyezési korlátok miatt a lehető legjobb egyenleggel rendelkezik. Ennek eredményeképpen megpróbálja kiegyensúlyozni a fürtöt, ami szükségtelen szolgáltatásmozgásokat okozhat (mivel az "egyensúlyhiány" nem javítható lényegesen). Ettől a kiadástól kezdve a fürterőforrás-kezelő most megpróbálja automatikusan észlelni az ilyen típusú konfigurációkat, és megérteni, hogy mikor lehet a mozgással javítani az egyensúlytalanságot, és ehelyett miért kell békén hagynia a dolgokat, mivel nem lehet jelentős javulást elérni.  
- [**A másodlagos replikák különböző áthelyezési költsége:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost)Új áthelyezési költségértéket vezettünk be VeryHigh, amely bizonyos esetekben további rugalmasságot biztosít annak meghatározásához, hogy a másodlagos replikákhoz külön áthelyezési költséget kell-e használni.
- Engedélyezett [**Liveness Probe**](https://docs.microsoft.com/azure/service-fabric/probes-codepackage ) mechanizmus konténeres alkalmazásokhoz. A Liveness Probe segít bejelenteni a tárolóba rendezett alkalmazás élőségét, és ha nem válaszolnak időben, az újraindítást eredményez.
- [**Futtatás a teljesítésig/egyszer a szolgáltatásokhoz**](https://docs.microsoft.com/azure/service-fabric/run-to-completion)**

### <a name="image-store-improvements"></a>A Képtár fejlesztései
 - A Service Fabric 7.1 alapértelmezés szerint egyéni átvitelt használ **a csomópontok közötti fájlátvitel biztosítására.** Az SMB fájlmegosztástól való függőség törlődik a 7.1-es verzióról. A biztonságos SMB fájlmegosztások továbbra is léteznek az Image Store Szolgáltatás replikáját tartalmazó csomópontokon, mivel az ügyfél leiratkozik az alapértelmezett beállításról, valamint a frissítésről és a régi verzióra való visszaminősítésről.
       
 ### <a name="reliable-collections-improvements"></a>Megbízható gyűjtemények fejlesztések

- [**A memóriában csak tárolja támogatja az állapotalapú szolgáltatások megbízható gyűjtemények:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-work-with-reliable-collections#volatile-reliable-collections)Illékony megbízható gyűjtemények lehetővé teszi, hogy az adatok megmaradnak a lemezen a tartósság ellen, nagy üzemkimaradások ellen, használható számítási feladatok, például a replikált gyorsítótár, például ahol alkalmi adatvesztés tolerálható. Az [illékony megbízható gyűjtemények korlátai és korlátozásai](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections-guidelines#volatile-reliable-collections)alapján ezt javasoljuk olyan számítási feladatokhoz, amelyeknem igényelnek megőrzést, a Quorum Loss ritka alkalmait kezelő szolgáltatásokesetében.
- [**Előzetes verzió: Service Fabric backup explorer:**](https://github.com/microsoft/service-fabric-backup-explorer)A Megbízható gyűjtemények biztonsági mentések kezelése érdekében a Service Fabric állapotalapú alkalmazások, Service Fabric Backup Explorer lehetővé teszi a felhasználók számára, hogy
    - A megbízható gyűjtemények tartalmának ellenőrzése és felülvizsgálata,
    - Az aktuális állapot frissítése konzisztens nézetre
    - A megbízható gyűjtemények aktuális pillanatképének biztonsági másolatának létrehozása
    - Az adatsérülés javítása
                 
### <a name="service-fabric-71-releases"></a>Service Fabric 7.1-es kiadásai
| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2020. április 20. | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7.0

Az Azure Service Fabric 7.0 már elérhető! A 7.0-s frissítés az Azure Portalon keresztül vagy egy Azure Resource Manager-telepítésen keresztül frissülhet. Az ünnepi időszak körüli kiadásokra vonatkozó vásárlói visszajelzések miatt nem kezdjük el automatikusan frissíteni azokat a fürtöket, amelyek januárig automatikus frissítéseket kapnak.
Januárban folytatjuk a szabványos bevezetési eljárást, és az automatikus frissítéssel rendelkező fürtök automatikusan megkapják a 7.0-s frissítést. Még egy bejelentést teszünk, mielőtt a bevezetés megkezdődik.
A tervezett megjelenési dátumokat is frissítjük, jelezve, hogy figyelembe vesszük ezt a szabályzatot. Keresse meg itt a frissítéseket a mi [jövőbeni kiadási menetrendek](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule).
 
Ez a Service Fabric legújabb kiadása, és kulcsfontosságú funkciókkal és fejlesztésekkel van megtöltve.

### <a name="key-announcements"></a>Legfontosabb közlemények
 - [**KeyVaultReference támogatja az alkalmazástitkos kulcsok (Előzetes verzió)**](https://docs.microsoft.com/azure/service-fabric/service-fabric-keyvault-references): Service Fabric-alkalmazások, amelyek engedélyezték [a felügyelt identitások](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) most közvetlenül hivatkozhat a Key Vault titkos URL-címet, mint egy környezeti változó, alkalmazás paraméter, vagy a tároló tárház hitelesítő adatait. A Service Fabric automatikusan feloldja a titkos kulcsot az alkalmazás felügyelt identitásával. 
     
- **Továbbfejlesztett frissítési biztonság állapotmentes szolgáltatások:** Az alkalmazásfrissítés során a rendelkezésre állás biztosítása érdekében új konfigurációkat vezettünk be, hogy meghatározzuk az [állapotmentes szolgáltatások minimális számú,](https://docs.microsoft.com/dotnet/api/system.fabric.description.statelessservicedescription?view=azure-dotnet) elérhetőnek tekinthető példányait. Korábban ez az érték 1 volt az összes szolgáltatásra, és nem volt módosítható. Ezzel az új szolgáltatásonkénti biztonsági ellenőrzéssel biztosíthatja, hogy a szolgáltatások megtartsák az alkalmazásfrissítések, a fürtfrissítések és a Service Fabric egészségügyi és biztonsági ellenőrzésén alapuló egyéb karbantartások során a felfelé álló példányok minimális számát.
  
- [**Erőforrás-korlátok a felhasználói szolgáltatások:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-resource-governance#enforcing-the-resource-limits-for-user-services)A felhasználók beállíthatnak erőforráskorlátokat a felhasználói szolgáltatások egy csomóponton, hogy megakadályozzák a forgatókönyvek, például az erőforrás-kimerülése a Service Fabric rendszerszolgáltatások. 
  
- [**Nagyon magas szolgáltatásáthelyezési költség**](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-resource-manager-movement-cost) egy replikatípushoz. A nagyon magas áthelyezési költséggel rendelkező replikák csak akkor kerülnek áthelyezésre, ha a fürtben olyan megkötés-megsértés van, amely semmilyen más módon nem javítható. Kérjük, olvassa el a dokumentumokat további információkért arról, hogy a "Nagyon magas" költözési költség használata mikor ésszerű, és további szempontokat is tartalmaz.
  
-  **További fürtbiztonsági ellenőrzések**: Ebben a kiadásban bevezettük a konfigurálható magnode kvórum biztonsági ellenőrzését. Ez lehetővé teszi, hogy testre szabhatja, hogy hány magcsomópontok kell elérhetővé a fürt életciklusa és felügyeleti forgatókönyvek során. Azok a műveletek, amelyek a fürtöt a beállított érték alá vennék, le vannak tiltva. Ma az alapértelmezett érték mindig a magcsomópontok kvóruma, például ha 7 magcsomóval rendelkezik, egy olyan művelet, amely 5 magcsomópont alá kerülne, alapértelmezés szerint blokkolva lenne. Ezzel a módosítással a minimális biztonságos értéket 6 teheti meg, amely egyszerre csak egy magcsomópontot engedélyezne.
   
- A Service Fabric Explorer ben a Biztonsági mentés és visszaállítás szolgáltatás kezeléséhez nyújtott támogatás [**hozzáadva.**](https://docs.microsoft.com/azure/service-fabric/service-fabric-backuprestoreservice-quickstart-azurecluster) Ez lehetővé teszi a következő tevékenységeket közvetlenül az SFX-en belülről: a biztonsági mentési és visszaállítási szolgáltatás felderítése, biztonsági mentési házirend létrehozása, automatikus biztonsági mentések engedélyezése, adhoc biztonsági mentések készítése, visszaállítási műveletek indításáa és a meglévő biztonsági mentések böngészése.

- A [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)elérhetőségének bejelentése: Ez az eszköz segít annak ellenőrzésében, hogy a megbízható gyűjteményekben használt típusok előre és hátra kompatibilisek-e a működés közbeni alkalmazásfrissítés során. Ez segít megelőzni a frissítési hibákat vagy az adatvesztést és az adatsérülést a hiányzó vagy nem kompatibilis típusok miatt.

- [**Engedélyezze a másodlagos replikák stabil olvasásait:**](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-configuration#configuration-names-1)A stabil olvasások a másodlagos replikákat a kvórum-kosztott értékekre korlátozzák.

Ezenkívül ez a kiadás más új funkciókat, hibajavításokat, valamint támogathatósági, megbízhatósági és teljesítménybeli fejlesztéseket is tartalmaz. A módosítások teljes listáját a [kibocsátási megjegyzésekben](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)tájékozatja.

### <a name="service-fabric-70-releases"></a>Service Fabric 7.0-s kiadásai

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2019. november 18. | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 2020. január 30. | [Az Azure Service Fabric 7.0 frissítési kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 2020. február 6. | [Az Azure Service Fabric 7.0 frissítési kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2020. március 2. | [Az Azure Service Fabric 7.0 frissítési kiadása](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)

### <a name="service-fabric-65"></a>Szolgáltatás Fabric 6,5

Ez a kiadás tartalmazza a támogathatóság, a megbízhatóság és a teljesítmény javítása, az új funkciók, hibajavítások, és a fejlesztések megkönnyítése fürt és az alkalmazás életciklus-kezelés.

> [!IMPORTANT]
> Service Fabric 6.5 a végleges kiadás a Service Fabric eszközök támogatása a Visual Studio 2015.Service Fabric 6.5 is the final release with Service Fabric tools support in Visual Studio 2015. Azt tanácsoljuk az ügyfeleknek, hogy a jövőben a [Visual Studio 2019-be](https://visualstudio.microsoft.com/vs/) költözzenek.

A Service Fabric 6.5 újdonságai:

- A Service Fabric Explorer tartalmaz egy [Image Store-megjelenítőt](service-fabric-visualizing-your-cluster.md#image-store-viewer) a képtárolóba feltöltött alkalmazások vizsgálatához.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) [1.4.0-s](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) verziója számos öndiagnosztikai fejlesztést tartalmaz. A POA ügyfeleinek ajánlott áthelyezni erre a verzióra.

- [Az EventStore Szolgáltatás alapértelmezés szerint engedélyezve van](service-fabric-visualizing-your-cluster.md#event-store) a Service Fabric 6.5-ös fürtökhöz, kivéve, ha leiratkozott.

- Hozzáadott [replika életciklus-események](service-fabric-diagnostics-event-generation-operational.md#replica-events) állapotalapú szolgáltatásokhoz.

- [A magcsomópont állapotának jobb láthatósága](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), beleértve a fürtszintű figyelmeztetéseket, ha egy magcsomópont nem kifogástalan (*Le*, *Eltávolítva* vagy *Ismeretlen*).

- [A Service Fabric alkalmazásvész-helyreállítási eszköz](https://github.com/Microsoft/Service-Fabric-AppDRTool) lehetővé teszi, hogy a Service Fabric állapotalapú szolgáltatások gyorsan helyreálljanak, ha az elsődleges fürt katasztrófába ütközik. Az elsődleges fürtből származó adatok folyamatosan szinkronizálva vannak a másodlagos készenléti alkalmazásban az időszakos biztonsági mentés és visszaállítás használatával.

- A Visual Studio támogatja a [.NET Core alkalmazások Linux alapú fürtökben való közzétételét.](service-fabric-how-to-publish-linux-app-vs.md)

- [Az Azure Service Fabric CLI (SFCTL)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) automatikusan települ a Service Fabric 6.5 (és újabb verziók) számára, amikor új Linux-fürtöt frissít vagy hoz létre az Azure-ban.

- [Az SFCTL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) alapértelmezés szerint MacOS/Linux OneBox fürtökre van telepítve.

További részletek a [Service Fabric 6.5 kiadási megjegyzések című témakörben talál.](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)

### <a name="service-fabric-65-releases"></a>Service Fabric 6.5-ös kiadásai

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2019. június 11.June 11, 2019 | [Azure Service Fabric 6.5](https://blogs.msdn.microsoft.com/azureservicefabric/2019/06/11/azure-service-fabric-6-5-release/)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2019. július 2.July 2, 2019 | [Az Azure Service Fabric 6.5 frissítési kiadása](https://blogs.msdn.microsoft.com/azureservicefabric/2019/07/04/azure-service-fabric-6-5-refresh-release/)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 2019. július 29. | [Az Azure Service Fabric 6.5 frissítési kiadása](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| 2019. augusztus 23.Aug 23, 2019 | [Az Azure Service Fabric 6.5 frissítési kiadása](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Kibocsátási megjegyzések](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 2019. október 14. | [Az Azure Service Fabric 6.5 frissítési kiadása](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Kibocsátási megjegyzések] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


## <a name="previous-versions"></a>Korábbi verziók

### <a name="service-fabric-64-releases"></a>Service Fabric 6.4-es kiadásai

| Kiadási dátum | Kiadás | További információ |
|---|---|---|
| 2018. november 30., | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 2018. december 12.December 12.December 12.December 12, 2018 | [Az Azure Service Fabric 6.4 frissítési kiadása Windows-fürtökhöz](https://blogs.msdn.microsoft.com/azureservicefabric/2018/12/12/azure-service-fabric-6-4-refresh-for-windows-clusters/)  | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 2019. február 4.February 4, 2019 | [Az Azure Service Fabric 6.4 frissítési kiadása](https://blogs.msdn.microsoft.com/azureservicefabric/2019/02/04/azure-service-fabric-6-4-refresh-release/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 2019. március 4., | [Az Azure Service Fabric 6.4 frissítési kiadása](https://blogs.msdn.microsoft.com/azureservicefabric/2019/03/12/azure-service-fabric-6-4-refresh-release-2/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 2019. április 8.April 8, 2019 | [Az Azure Service Fabric 6.4 frissítési kiadása](https://blogs.msdn.microsoft.com/azureservicefabric/2019/04/08/azure-service-fabric-6-4-refresh-release-5/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2019. május 2.May 2, 2019 | [Az Azure Service Fabric 6.4 frissítési kiadása](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/02/azure-service-fabric-6-4-refresh-release-3/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 2019. május 28.May 28, 2019 | [Az Azure Service Fabric 6.4 frissítési kiadása](https://blogs.msdn.microsoft.com/azureservicefabric/2019/05/28/azure-service-fabric-6-4-refresh-release-4/) | [Kibocsátási megjegyzések](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
