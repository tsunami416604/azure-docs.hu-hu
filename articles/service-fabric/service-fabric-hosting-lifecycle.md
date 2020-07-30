---
title: Az Azure Service Fabric az aktiválási és inaktiválási életciklust üzemelteti
description: Ismerteti az alkalmazások és a szervizcsomagok életciklusát egy csomóponton
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: a39aecf16d1c3303c0a590b389ba2aa69d4472f2
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405126"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric üzemeltetési életciklus
Ez a cikk áttekintést nyújt azokról az eseményekről, amelyek akkor történnek, amikor egy alkalmazás aktiválva van egy csomóponton, és különböző fürtkonfiguráció van használatban a viselkedés szabályozására.

A folytatás előtt győződjön meg arról, hogy tisztában van a különböző fogalmakkal és kapcsolatokkal az [alkalmazás modellezése Service Fabricban][a1]. 

> [!NOTE]
> Ebben a cikkben, kivéve, ha explicit módon említi, hogy valami más:
>
> - A *replika* egy állapot-nyilvántartó szolgáltatás replikáját és egy állapot nélküli szolgáltatás egy példányát jelenti.
> - A *CodePackage* egy *ServiceHost* -folyamattal egyenértékűként kezeli a rendszer, amely regisztrálja a *ServiceType*, és az adott *ServiceType*tartozó szolgáltatások replikáit tárolja.
>

## <a name="activation-of-applicationservicepackage"></a>Alkalmazás/szervizcsomag aktiválása

Az aktiválási folyamat a következő:

1. Töltse le a ApplicationPackage. Például: ApplicationManifest.xml stb.
2. Környezet beállítása az alkalmazáshoz: felhasználók létrehozása stb.
3. Az inaktiváláshoz tartozó alkalmazás követésének megkezdése.
4. Töltse le a szervizcsomagot. Például: ServiceManifest.xml, kód, konfiguráció, adatcsomagok stb.
5. Környezet beállítása a szervizcsomaghoz: telepítési tűzfal, portok lefoglalása a végpontokhoz stb.
6. Megkezdheti az inaktiváláshoz szükséges szervizcsomagok nyomon követését.
7. Indítsa el a CodePackages SetupEntryPoint, és várjon a befejezésig.
8. A CodePackages MainEntryPoint elindítása.

### <a name="servicetype-blocklisting"></a>ServiceType Blocklisting
A **ServiceTypeDisableFailureThreshold** meghatározza a hibák (aktiválás, letöltés, CodePackage hibák) számát, amely után a ServiceType ütemezve van a blocklisting. Az első aktiválási/letöltési hiba vagy a CodePackage összeomlása esetén az ServiceType-blocklisting ütemtervét kell elindítania. A **ServiceTypeDisableGraceInterval** konfiguráció határozza meg azt a türelmi intervallumot, amely után a ServiceType végül a csomópont blocklisted van megjelölve. Vegye figyelembe, hogy ehhez az aktiválási/letöltési/CodePackage újraindításhoz továbbra is újra kell próbálkoznia az újrapróbálkozási módban, és nyomon követheti az alrendszer üzemeltetését. Újrapróbálkozás, például: a CodePackage az összeomlás után újraindulnak, vagy Service Fabric megkísérli a csomagok újraletöltését.
A blocklisted után "" System. hosting "hibát jelzett a következő tulajdonságnál:" ServiceTypeRegistration: ServiceType ". A ServiceType le lett tiltva a csomóponton. "

A ServiceType ismét engedélyezve lesz a csomóponton. 
- Ha az aktiválási művelet sikeres, vagy elérte a **ActivationMaxFailureCount** újrapróbálkozást a hiba esetén.
- Ha a letöltési művelet sikeres, vagy elérte a **DeploymentMaxFailureCount** újrapróbálkozást a hiba esetén.
- Ha egy összeomlást okozó CodePackage megkezdi a biztonsági mentést, és sikeresen regisztrálja a ServiceType.

A ServiceType újbóli engedélyezésének oka az **ActivationMaxFailureCount**- / **DeploymentMaxFailureCount** újrapróbálkozása után, a maximális próbálkozások Service Fabric a csomóponton lévő alkalmazások aktiválásához és letöltéséhez. Ha nem sikerül, az aktuális művelet nem próbálkozik újra, és mivel Service Fabric szeretné, hogy a szolgáltatás egy másik lehetőséget biztosítson az aktiválásra, ami sikeres lehet, ami a probléma automatikus gyógyulását eredményezi, az aktiválási/letöltési művelet életciklusához van kötve. Egy replika elhelyezésével aktivált új aktiválási/letöltési művelet újból elindíthatja a ServiceType blocklisting, vagy sikeres lehet.

> [!NOTE]
> Ha a CodePackage, amely nem regisztrálja a ServiceType, a rendszer összeomlik, nem befolyásolja a ServiceType. Csak a replika-összeomlást üzemeltető CodePackage befolyásolja a ServiceType.
>

### <a name="codepackage-crash"></a>CodePackage összeomlása
Ha egy CodePackage összeomlik, Service Fabric egy back-off használatával indítja el újra, és a back-off független attól, hogy a csomag regisztrálva van-e az USA-ban vagy sem.

A back-off érték mindig min (RetryTime, **ActivationMaxRetryInterval**), és ez az érték lehet állandó, lineáris vagy exponenciális a **ActivationRetryBackoffExponentiationBase** config alapján.

- Állandó: if **ActivationRetryBackoffExponentiationBase** = = 0, RetryTime = **ActivationRetryBackoffInterval**;
- Lineáris: Ha **ActivationRetryBackoffExponentiationBase** = = 0, akkor a RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** , ahol a ContinousFailureCount az a szám, ahányszor egy CodePackage összeomlik vagy nem sikerül aktiválni.
- Exponenciális: RetryTime = (**ActivationRetryBackoffInterval** másodpercben) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
A viselkedést a gyors újraindításhoz hasonlóan szabályozhatja. Beszéljünk a lineárisról. Ez azt jelenti, hogy ha egy CodePackage összeomlik, a kezdési időköz 10, 20 és 30 40 másodperc után lesz, amíg a CodePackage inaktiválva nem lesz. 
    
A maximális idő Service Fabric a biztonsági mentést (vár), miután a **ActivationMaxRetryInterval** a hibát szabályozza
    
Ha a CodePackage összeomlik, és biztonsági mentést készít, akkor a Service Fabric **CodePackageContinuousExitFailureResetInterval** kell maradnia, hogy a megfelelő állapotot adja meg, és állítsa vissza a ContinousFailureCount.

### <a name="codepackage-not-registering-servicetype"></a>A CodePackage nem regisztrálja a ServiceType
Ha egy CodePackage életben marad, és a rendszer azt várta, hogy egy ServiceType regisztrálnak az USA-ban, de soha nem, ebben az Service Fabric esetben a rendszer figyelmeztetési állapotjelentés hoz majd, miután **ServiceTypeRegistrationTimeout** azt, hogy a ServiceType nem az időtúllépésen belül lett konfigurálva.

### <a name="activation-failure"></a>Aktiválási hiba
A Service Fabric mindig lineáris visszalépést használ (ugyanaz, mint a CodePackage Crash), amikor hibát talál az aktiválás során. Ez azt jelenti, hogy az aktiválási művelet a következő után fog megjelenni: (0 + 10 + 20 + 30 + 40) = 100 másodperc (az első újrapróbálkozás azonnali). Az aktiválás után a rendszer nem próbálkozik újra.
    
Az aktiválási leállítási maximálisan **ActivationMaxRetryInterval** , és újrapróbálkozhat a **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Letöltési hiba
Service Fabric mindig lineáris biztonsági mentést használ, ha a letöltés során hiba lép fel. Ez azt jelenti, hogy az aktiválási művelet a következő után fog megjelenni: (0 + 10 + 20 + 30 + 40) = 100 másodperc (az első újrapróbálkozás azonnali). A letöltés után nem próbálkozik újra. A letöltés lineáris visszakapcsolási értéke a ContinuousFailureCount ***DeploymentRetryBackoffInterval** , és a maximálisan visszatérhet a **DeploymentMaxRetryInterval**. Az aktiválásokhoz hasonlóan a letöltési művelet is újra próbálkozik a **ActivationMaxFailureCount**.

> [!NOTE]
> A konfigurációk módosítása előtt tekintse át az alábbi példákat.

* Ha a CodePackage megtartja a összeomlik és a biztonsági mentést, a ServiceType le lesz tiltva. Ha azonban az aktiválások konfigurációja olyan, hogy gyorsan újraindul, akkor a CodePackage néhány alkalommal elérheti a ServiceType letiltását. Például: tegyük fel, hogy a CodePackage megérkezik, regisztrálja Service Fabric, majd összeomlik a ServiceType. Ebben az esetben az **ServiceTypeDisableGraceInterval** időszak megszakadása esetén a rendszer megszakítja a regisztráció típusát. Ez pedig megismételhető, amíg a CodePackage a **ServiceTypeDisableGraceInterval** -nál nagyobb értékre vált, majd a ServiceType le lesz tiltva a csomóponton. Ezért előfordulhat, hogy a ServiceType a csomóponton való letiltása előtt egy ideig lehet.

* Aktiválások esetén, amikor Service Fabric rendszernek replikát kell elhelyeznie egy csomóponton, az RA (ReconfigurationAgent) megkérdezi az alrendszert, hogy aktiválja az alkalmazást, és 15 másodpercenként (**RAPMessageRetryInterval**) újrapróbálkozik az aktiválási kéréssel. Ha Service Fabric rendszer tudni szeretné, hogy a ServiceType le lett tiltva, az üzemeltetési aktiválási műveletnek hosszabb ideig kell élnie, mint az újrapróbálkozási időköz és a **ServiceTypeDisableGraceInterval**. Tegyük fel például, hogy a fürtben a konfigurációk **ActivationMaxFailureCount** 5 értékre van állítva, és a **ActivationRetryBackoffInterval** értéke 1 MP. Ez azt jelenti, hogy az aktiválási művelet a következő után fog megjelenni: (0 + 1 + 2 + 3 + 4) = 10 mp (az első újrapróbálkozás azonnali), és ezt követően a gazdagép újrapróbálkozik. Ebben az esetben az aktiválási művelet befejezve lesz, és 15 másodperc elteltével nem próbálkozik újra. Ez azért történt, mert a Service Fabric 15 másodpercen belül kimerítette az összes újrapróbálkozást. Így a ReconfigurationAgent minden újrapróbálkozása új aktiválási műveletet hoz létre az alrendszer üzemeltetése során, és a minta megőrzi az ismétlődést, és a ServiceType soha nem lesz letiltva a csomóponton. Mivel a ServiceType nem fog letiltani a csomóponton, az SF rendszer Component FM (FailoverManager) nem helyezi át a replikát egy másik csomópontra.
> 

## <a name="deactivation"></a>Inaktiválása

Ha egy csomóponton aktiválják a szervizcsomagot, az inaktiválást nyomon követik. A deélénkítő az az entitás, amely nyomon követi a nyomkövetést.
A deaktiváló két módon működik:

1.  Időnként: minden **DeactivationScanInterval**ellenőrzi azokat a ServicePackages, amelyek soha nem üzemeltettek replikát, és az inaktiválásra jelöltként jelöli meg őket.
2.  ReplicaClose: Ha egy replika be van zárva, a Deactivator beolvas egy DecrementUsageCount. Ha a Count 0 értékre mutat, ez azt jelenti, hogy a szervizcsomag nem üzemeltet replikát, ezért az inaktiválásra jelölt.

 A [kizárólagos/megosztott][a2]aktiválási mód alapján a Deaktiválásra jelölt jelöltek ütemezése a SharedMode/ **ExclusiveModeDeactivationGraceInterval** for ExclusiveMode **DeactivationGraceInterval** után történik. Ha ebben az időszakban egy új replika elhelyezése történik, akkor a rendszer megszakítja az inaktiválást.

### <a name="periodically"></a>Rendszeresen
1. példa: tegyük fel, hogy a Deactivator a következő időpontban végez vizsgálatot: T (**DeactivationScanInterval**). A következő vizsgálat a következő időpontban fog megjelenni: 2T. Tegyük fel, hogy a szervizcsomag aktiválása a T + 1 időpontban történt. Ez a szervizcsomag nem üzemeltet replikát, ezért inaktiválva kell lennie. Ahhoz, hogy a szervizcsomagok inaktiválásra legyenek kijelöltként, a rendszernek nem kell replikát használnia a atleast T-időre vonatkozóan. Ez azt jelenti, hogy a rendszer a 2T + 1 címen inaktiválásra jogosult. Így a 2T-vizsgálat nem találja ezt a szervizcsomagot inaktiválásra jelöltként. A következő inaktiválási ciklus 3T ezt a szervizcsomagot inaktiválásra fogja ütemezni, mert most már nem volt replika állapotban a T ideje.  

2. példa: tegyük fel, hogy a rendszer a T-1 időpontban aktiválja a szervizcsomagot, és a deélénkítő ellenőrzi a T-T. A szervizcsomag nem üzemeltet replikát. Ezután a következő vizsgálat 2T ez a szervizcsomag inaktiválásra jelöltként lesz megtalálva, ezért az inaktiválásra lesz ütemezve.  

3. példa: tegyük fel, hogy egy szervizcsomag a T-1-ben aktiválva lesz, és a Deactivator a T-T vizsgálja. A szervizcsomag még nem üzemeltet replikát. Most a T + 1 replika lesz elhelyezve, azaz Az üzemeltetés egy IncrementUsageCount kap, ami azt jelenti, hogy létrejön egy replika. Most, 2T ez a szervizcsomag nem lesz ütemezve inaktiválásra. Az Inaktiválás most az alább ismertetett ReplicaClose logikára vált.

4. példa: tegyük fel, hogy a szervizcsomag nagy méretű, például 10 GB, és a csomóponton is eltarthat egy kis ideig. Az alkalmazás aktiválása után a Deactivator nyomon követi az életciklusát. Ha a **DeactivationScanInterval** -konfiguráció kicsi, akkor előfordulhat, hogy olyan problémákba ütközik, amelyekben a szervizcsomag nem kap időt az aktiválásra a csomóponton, mert az idő múlásával a letöltés már megtörtént. A probléma megoldása érdekében a [szervizcsomagot előre letöltheti a csomóponton][p1]. 

> [!NOTE]
> Így a szervizcsomagok bármikor inaktiválva lehetnek (**DeactivationScanInterval** – 2 ***DeactivationScanInterval**) + **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 
>

### <a name="replica-close"></a>Replika lezárása:
A Deactivator megtartja a szervizcsomagban tárolt replikák számát. Ha a szervizcsomag egy replikát tárol, és a replika le/le van zárva, a gazdagép DecrementUsageCount kap. Egy replika megnyitásakor a gazdagép IncrementUsageCount kap. A csökkentés azt jelenti, hogy a szervizcsomag jelenleg egy kevesebb replikát üzemeltet, és ha a számláló 0-ra csökken, akkor a szervizcsomag inaktiválásra van ütemezve, és az az idő, amely után az inaktiválva lesz, a **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

Például: tegyük fel, hogy a T és a (z) 2t + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**) esetében az inaktiválásra van ütemezve. Ha ez idő alatt a gazdagép egy IncrementUsage, ami azt jelenti, hogy létrejön egy replika, akkor a rendszer megszakítja az inaktiválást.

> [!NOTE]
>Tehát mit jelent ez a konfiguráció alapvetően a következőt jelenti: **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: az az idő, amelyet a szervizcsomagnak egy másik replikát kell üzemeltetni, miután a rendszer bármilyen replikát futtatott. 
**DeactivationScanInterval**: az a legkisebb idő, amelyet a szervizcsomagnak a replika üzemeltetéséhez kell adni, ha soha nem üzemeltetett replikát, azaz Ha nincs használatban.
>

### <a name="ctrlc"></a>Ctrl+C
Ha a szervizcsomag átadja a **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** , és továbbra sem replikát üzemeltet, az Inaktiválás nem visszavonhatatlan. A CodePackage egy CTRL + C kezelőt állítanak ki, amely azt jelenti, hogy most az inaktiválási folyamatnak végig kell haladnia, hogy leállítsa a folyamatot. Ebben az időszakban, ha az ugyanarra a szervizcsomagra vonatkozó új replika megpróbál bekerülni, a művelet sikertelen lesz, mert nem lehet áttérni az inaktiválásról az aktiválásra.

## <a name="cluster-configs"></a>Fürt konfigurációi

Az aktiválási/decativation befolyásoló alapértékekkel rendelkező konfigurációk.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: alapértelmezett 1. A hibák számának küszöbértéke, amely után az FM (FailoverManager) értesítést kap, hogy letiltsa a szolgáltatás típusát a csomóponton, és egy másik csomópontot próbál meg elhelyezésre.
**ServiceTypeDisableGraceInterval**: alapértelmezett 30 mp. az az időtartam, amely után a szolgáltatástípus le lehet tiltani.
**ServiceTypeRegistrationTimeout**: alapértelmezett 300 másodperc. A ServiceType Service Fabric való regisztrálásának időtúllépése.

### <a name="activation"></a>Aktiválás
**ActivationRetryBackoffInterval**: alapértelmezett 10 mp. leállítási időköz minden aktiválási hiba esetén.
**ActivationMaxFailureCount**: alapértelmezett 20. A maximális darabszám, amely után a rendszer újrapróbálkozik a sikertelen aktiválással. 
**ActivationRetryBackoffExponentiationBase**: alapértelmezett 1,5.
**ActivationMaxRetryInterval**: alapértelmezett 3600 mp. max vissza az aktiváláshoz a hibáknál.
**CodePackageContinuousExitFailureResetInterval**: alapértelmezett 300 másodperc. A CodePackage folyamatos kilépési hibáinak számának alaphelyzetbe állítására szolgáló időkorlát.

### <a name="download"></a>Letöltés
**DeploymentRetryBackoffInterval**: alapértelmezett 10. Az üzembe helyezési hiba időkorlátja.
**DeploymentMaxRetryInterval**: alapértelmezett 3600 mp. max. a telepítés sikertelen volt.
**DeploymentMaxFailureCount**: alapértelmezett 20. Az alkalmazás központi telepítése újra próbálkozik a DeploymentMaxFailureCount, mielőtt az alkalmazás telepítése a csomóponton meghiúsul.

### <a name="deactivation"></a>Inaktiválása
**DeactivationScanInterval**: alapértelmezett 600 másodperc. a szervizcsomag tárolására szolgáló minimális idő, ha még soha nem üzemeltetett replikát, azaz Ha nincs használatban.
**DeactivationGraceInterval**: alapértelmezett 60 másodperc. A szervizcsomagnak egy másik replika futtatására adott idő, ha **megosztott** négyszínes modell esetén replikát üzemeltetett.
**ExclusiveModeDeactivationGraceInterval**: alapértelmezett 1 MP. Az az idő, amelyet a szervizcsomagnak egy másik replikát kell üzemeltetni, ha a **kizárólagos** folyamatmodell esetében bármilyen replikát futtatott.

## <a name="next-steps"></a>Következő lépések
[Alkalmazás becsomagolása][a3] és üzembe helyezése készen áll a telepítésre.

[Alkalmazások telepítése és eltávolítása][a4]. Ez a cikk azt ismerteti, hogyan használható a PowerShell az alkalmazások példányainak kezeléséhez.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
