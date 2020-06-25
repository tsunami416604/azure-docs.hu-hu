---
title: Azure AD Connect Sync v2-végpont nyilvános előzetes verziója | Microsoft Docs
description: Ez a dokumentum az Azure AD összekapcsolási szinkronizálási v2-végpontok API frissítéseit ismerteti.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a2e8bb6da4cf126a9dbd955b082d77965772f6f
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85357579"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api-public-preview"></a>Azure AD Connect Sync v2 Endpoint API (nyilvános előzetes verzió) 
A Microsoft új végpontot (API-t) helyezett üzembe a Azure AD Connect számára, amely javítja a szinkronizálási szolgáltatás műveleteinek teljesítményét Azure Active Directory. Az új v2-végpont kihasználása az Azure AD-ba való exportálás és importálás során észrevehető teljesítménybeli nyereséget tapasztal. Ez az új végpont a következőket támogatja:
    
 -  csoportok szinkronizálása legfeljebb 250k taggal
 - teljesítménybeli nyereség az Azure AD-ba való exportálás és importálás során
 
> [!NOTE]
> Jelenleg az új végpont nem rendelkezik konfigurált csoportra vonatkozó méretkorlát a visszaállított O365-csoportokhoz. Ez hatással lehet a Active Directoryra és a szinkronizálási ciklus késésére.  A csoportok méretének növekményes növelését javasoljuk.  


## <a name="pre-requisites"></a>Előfeltételek  
Az új v2-végpont használatához [Azure ad Connect 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) vagy újabb verziót kell használnia, és az alábbi üzembe helyezési lépések végrehajtásával engedélyezheti a v2-végpontot a Azure ad Connect-kiszolgálóhoz.   

>[!NOTE]
>Ez a nyilvános előzetes verzió jelenleg csak az Azure globális felhőben érhető el, és nem érhető el az [országos felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)számára.

### <a name="public-preview-limitations"></a>A nyilvános előzetes verzió korlátozásai  
Habár ez a kiadás kiterjedt tesztelésen esett át, még problémák merülhetnek fel. A nyilvános előzetes kiadás egyik célja, hogy megkeresse és javítsa ki az ilyen problémákat.  

>[!IMPORTANT]
> A nyilvános előzetes kiadáshoz nyújtott támogatás mellett a Microsoft nem mindig tudja elhárítani az összes olyan problémát, amely azonnal felmerülhet. Ezért ajánlott a legjobb ítéletet használni, mielőtt üzembe helyezné a kiadást az éles környezetben. 

## <a name="deployment-guidance"></a>Üzembe helyezési útmutató 
A v2-végpont használatához telepítenie kell [Azure ad Connect 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) -es vagy újabb verzióját. Használja a letöltéshez megadott hivatkozást. 

Javasoljuk, hogy kövesse a [swing áttelepítési](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration) módszert az új végpontnak a környezetében való működéséhez. Ez egyértelmű készenléti tervet biztosít az eseményen, amely jelentős visszaállítást tesz szükségessé. Az alábbi példa azt szemlélteti, hogyan használható a swing Migrálás ebben a forgatókönyvben. A swing Migration üzembe helyezési módszerével kapcsolatos további információkért tekintse meg a megadott hivatkozást. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Swing Migrálás a v2-végpont üzembe helyezéséhez
A következő lépések végigvezetik a v2-végpont üzembe helyezésének lépésein a swing metódus használatával.

1. Telepítse a v2-végpontot az aktuális átmeneti kiszolgálón. Ezt a kiszolgálót az alábbi lépésekben a **v2-kiszolgálóként** fogja tudni. A jelenlegi aktív kiszolgáló a v1 végpont használatával továbbra is feldolgozza az üzemi munkaterhelést, amely az alábbi **v1-kiszolgáló** lesz.
1. Győződjön meg arról, hogy a **v2-kiszolgáló** továbbra is a várt módon dolgozza fel az importálásokat. Ebben a szakaszban a nagyméretű csoportok nem lesznek kiépítve az Azure AD-be vagy a helyszíni AD-be, de ellenőrizheti, hogy a frissítés nem eredményezett-e váratlan hatást a meglévő szinkronizálási folyamatra. 
2. Az érvényesítés befejeződése után állítsa a **v2-kiszolgálót** az aktív kiszolgálóra és az átmeneti kiszolgálóként működő **v1** -kiszolgálóra. Jelenleg a szinkronizálni kívánt nagyméretű csoportok lesznek kiépítve az Azure AD-be, valamint a nagyméretű O365 egyesített csoportok az AD-ben lesznek kiépítve, ha a csoport visszaírási engedélyezve van.
3. Ellenőrizze, hogy a **v2-kiszolgáló** sikeresen végrehajtja és feldolgozza-e a nagyméretű csoportokat. Dönthet úgy, hogy megmarad ebben a lépésben, és figyeli a szinkronizálási folyamatot egy adott időszakra vonatkozóan.
  >[!NOTE]
  > Ha vissza kell térnie az előző konfigurációra, a **v2-kiszolgálóról** a **v1-kiszolgálóra**történő áttelepítést is végrehajthatja. Mivel a v1-végpont nem támogatja a több mint 50 000 taggal rendelkező csoportokat, az Azure AD-ben vagy a helyszíni AD-ben az Azure AD Connect által kiépített nagy csoportok később törlődni fognak. 
4. Ha biztos abban, hogy a v2-végpontot használja, frissítse a **v1-kiszolgálót** a v2-végpont használatának megkezdéséhez. 
 

## <a name="expectations-of-performance-impact"></a>A teljesítményre gyakorolt hatásra vonatkozó elvárások  
A v2-végpont használatakor a teljesítmény a szinkronizált csoportok számának, a csoportok méretének és a csoport adatforgalmának függvénye (a felhasználók a csoport tagjaiként való hozzáadásával és eltávolításával létrejövő tevékenység). Az új végpont használata a szinkronizált csoportok számának, méretének vagy elváltozásának növelése nélkül az Azure AD-be való exportáláshoz és importáláshoz rövidebb időt eredményez. 
 
A teljesítménybeli nyereség azonban a nagyméretű csoportok szinkronizálásához szükséges további feldolgozással is megtagadható. Ha túl sok nagy csoportot ad hozzá a szinkronizálási folyamathoz, a teljes szinkronizálási időt növelheti.  

Ha jobban meg szeretné ismerni, hogy az új csoportok hozzáadása milyen hatással lesz a szinkronizálási teljesítményre, javasoljuk, hogy először csak néhány nagyobb, 100 000 taggal rendelkező csoportot szinkronizáljon. A csoportok számának és méretének növeléséhez a hatókör, a szervezeti egység, az attribútum vagy a csoport maximális méretének szűrése révén növelheti a csoportok számát és méretét. A teljesítménnyel kapcsolatos fejlemények az Azure AD-összekötő exportálási és importálási feladataiban, nem pedig a helyszíni AD-összekötőn lesznek megvalósítva. 

## <a name="deployment-step-by-step"></a>Üzembe helyezés lépésről lépésre 
A következő három fázis részletesen szemlélteti az új v2-végpont üzembe helyezését.  A fázisokat útmutatóként használhatja az üzembe helyezéshez.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>1. fázis – Azure AD Connect telepítése és ellenőrzése 
Javasoljuk, hogy először hajtsa végre a következő lépéseket a telepítéshez vagy a frissítéshez Azure AD Connect 1.5.30.0-es vagy újabb [verzióra](https://www.microsoft.com/download/details.aspx?id=47594) , majd ellenőrizze a szinkronizálási folyamatot, mielőtt megkezdi a v2-végpont engedélyezését. A Azure AD Connect-kiszolgálón: 


1. Választható Adatbázis biztonsági másolatának készítése 
2. Telepítse vagy frissítsen [Azure ad Connect 1.5.30.0 vagy újabb verzióra](https://www.microsoft.com/download/details.aspx?id=47594) .
3. A telepítés ellenőrzése 

### <a name="phase-2--enable-the-v2-endpoint"></a>2. fázis – a v2-végpont engedélyezése 
A következő lépés a v2-végpont engedélyezése. 

> [!NOTE]
> Miután engedélyezte a v2-végpontot a kiszolgáló számára, megtekintheti a meglévő számítási feladatok teljesítményének változásait. A csoportok még nem lesznek képesek az 50 000 tagúnál nagyobb csoportok szinkronizálására. 

A v2-végpontra való váltáshoz kövesse az alábbi lépéseket: 

1. Nyisson meg egy PowerShell-parancssort rendszergazdaként. 
2. A szinkronizálási ütemező letiltása, miután meggyőződött arról, hogy nem futnak szinkronizációs műveletek: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Importálja az új modult: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Váltson a v2-végpontra:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
Ezzel engedélyezte a kiszolgáló v2-végpontját. Némi időbe telik annak ellenőrzése, hogy nincs-e váratlan eredmény a v2-végpont engedélyezése után, mielőtt továbblép a következő fázisra, ahol megnöveli a csoport méretének korlátját. 
>[!NOTE]
>A fájl/modul elérési útjai eltérő meghajtóbetűjelet használhatnak a Azure AD Connect telepítésekor megadott telepítési útvonaltól függően. 


### <a name="phase-3--increase-the-group-membership-limit"></a>3. fázis – a csoporttagság korlátjának emelése 
Miután meggyőződött róla, hogy a szolgáltatás váratlan eredmények nélkül fut, folytathatja a csoporttagság korlátjának növelését. Javasoljuk, hogy először emelje fel a tagsági korlátot egy valamivel magasabb értékre, e g-ra. a 75K tagjai az Azure AD-vel szinkronizált nagyobb csoportok megtekintéséhez. Ha elégedett az eredménnyel, tovább növelheti a tag korlátját.  

A maximális korlát a 250K tagjai csoportonként. 

A tagsági korlát növeléséhez a következő lépések használhatók:  

1. Az Azure AD synchronization Rules Editor megnyitása 
2. A szerkesztőben válassza a **kimenő** lehetőséget az irányhoz 
3. Kattintson a **out to HRE – csoportos csatlakozás** szinkronizálása szabályra 
4. Kattintson a **Szerkesztés** gombra a ![ szinkronizálási szabály szerkesztése elemre.](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Kattintson az **Igen** gombra az alapértelmezett szabály letiltásához és a szerkeszthető másolat létrehozásához.
 ![Szinkronizálási szabály szerkesztése](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. A **Leírás** lapon lévő előugró ablakban állítsa be a sorrendet egy 1 és 99 közötti értékre, és ![ módosítsa a szinkronizálási szabály értékét.](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Az **átalakítások** lapon frissítse a **tag** átalakításának **forrás** értékét, és cserélje le a 50000 értéket a 50001 és 250000 közötti értékre. Ez a csere növeli az Azure AD-vel szinkronizálni kívánt csoportok maximális tagsági méretét. Azt javasoljuk, hogy a nagyszámú 100k megkezdése után a nagy csoportok szinkronizálásának hatása legyen a szinkronizálási teljesítményre. 
 
 **Példa** 
 
 `IIF((ValueCount("member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Szinkronizálási szabály szerkesztése](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Kattintson a Save (Mentés) gombra. 
10. Rendszergazdai PowerShell-Parancssor megnyitása 
11. A szinkronizálási ütemező újbóli engedélyezése 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Ha Azure AD Connect Health nincs engedélyezve, módosítsa a Windows-alkalmazás eseménynaplójának beállításait úgy, hogy felülírja a naplókat a felülírás helyett. A naplók felhasználhatók a jövőbeli hibaelhárítási erőfeszítések segítésére. 

>[!NOTE]
> Az új végpont engedélyezése után további exportálási hibák jelenhetnek meg a "DN-attributes-Error" nevű HRE-összekötőn. A rendszer a 6949 azonosítóval ellátott hibákhoz tartozó eseménynapló-bejegyzést is megadja. A hibák tájékoztató jellegűek, és nem jeleznek problémát a telepítés során, hanem a szinkronizálási folyamat nem tudott felvenni bizonyos tagokat egy csoportba az Azure AD-ben, mert maga a tag objektum nem lett szinkronizálva az Azure AD-vel. 

Az új v2-végponti kód néhány típusú exportálási hibát kezel, némileg eltérve a v1-kódnak.  Ha a v2-végpontot használja, a további tájékoztató hibaüzenetek is megjelennek. 

>[!NOTE]
> Azure AD Connect frissítésekor győződjön meg arról, hogy a 2. fázisban szereplő lépések újra lesznek futtatva, mivel a módosítások nem őrződnek meg a frissítési folyamat során. 

A **HRE – csoportos csatlakozás** szinkronizálása szabályban a csoporttag korlátjának későbbi növekedése során nincs szükség teljes szinkronizálásra, ezért a következő parancs a PowerShellben való futtatásával letilthatja a teljes szinkronizálást. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Ha több mint 50 000 taggal rendelkező egységes O365 rendelkezik, a rendszer beolvassa a csoportokat Azure AD Connectba, és ha engedélyezve van a csoport visszaírási, a rendszer a helyszíni AD-be fogja írni. 

## <a name="rollback"></a>Visszaállítási 
Ha engedélyezte a v2-végpontot, és vissza kell állítania a visszaállítást, kövesse az alábbi lépéseket: 

1. A Azure AD Connect-kiszolgálón: a. Választható Adatbázis biztonsági másolatának készítése 
2. Nyisson meg egy rendszergazdai PowerShell-parancssort:
3. A szinkronizálási ütemező letiltása annak ellenőrzése után, hogy nincs-e folyamatban szinkronizációs művelet
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Váltson a v1-végpontra * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Az Azure AD synchronization Rules Editor megnyitása 
6. Törölje a **HRE – csoportos csatlakozás** szinkronizálása szabály szerkeszthető példányát 
7. A **kimenő HRE – csoportos csatlakozás** szinkronizálási szabályának alapértelmezett példányának engedélyezése 
8. Rendszergazdai PowerShell-Parancssor megnyitása 
9. A szinkronizálási ütemező újbóli engedélyezése 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Ha a v2 – v1 végpontokra vált vissza, a rendszer a teljes szinkronizálás futtatása után törli a több mint 50 000 taggal szinkronizált csoportokat, mind az Azure AD-hez kiépített AD-csoportok, mind az AD-hez kiépített egyesített csoportok O365. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések  
**K: az ügyfél használhatja ezt a szolgáltatást éles környezetben?**  
</br>Igen, ez éles környezetben is használható, a korábban említett kikötéssel.
 
**K: ki tud kapcsolatba lépni az ügyféllel, ha a dolgok helytelenek?**  
</br>Ha a funkció használatakor támogatásra van szüksége, nyisson meg egy támogatási esetet. 
 
**K: várhatok gyakori frissítéseket a nyilvános előzetes verzióra?**  
</br>A nyilvános előzetes verzióban korlátozott mértékű folyamatos változások történtek.A nyilvános előzetes verziójú szolgáltatások éles üzembe helyezése során érdemes felmérni ezt a kockázatot.  
 
**K: a következő mérföldkőre való idő?**  
</br>A nyilvános előzetes verzió funkcióit a további mérföldkövek elérése előtt lehet visszavonni és valószínűleg újratervezni.  
 
## <a name="next-steps"></a>További lépések

* [Azure AD Connect szinkronizálás: a szinkronizálás megismerése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
