---
title: Az Azure Service Fabric az aktiválási és inaktiválási életciklust üzemelteti
description: Ismerteti az alkalmazások és a szervizcsomagok életciklusát egy csomóponton
author: tugup
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: tugup
ms.openlocfilehash: f049b19703d37412d1ee1961aee6cb327efabe7c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779600"
---
# <a name="azure-service-fabric-hosting-lifecycle"></a>Azure Service Fabric üzemeltetési életciklus

Ez a cikk áttekintést nyújt az Azure Service Fabricban megjelenő eseményekről, amikor egy alkalmazás aktiválva van egy csomóponton, és különböző, a viselkedés szabályozására használt fürtkonfiguráció.

A folytatás előtt győződjön meg arról, hogy tisztában van a különböző fogalmakkal és kapcsolatokkal az [alkalmazás modellezése Service Fabricban][a1]. 

> [!NOTE]
> Ebben a cikkben, kivéve, ha explicit módon említi, hogy valami más:
>
> - A *replika* egy állapot-nyilvántartó szolgáltatás replikáját és egy állapot nélküli szolgáltatás egy példányát jelenti.
> - A *CodePackage* egy *ServiceHost* -folyamattal egyenértékűként kezeli a rendszer, amely regisztrálja a *ServiceType*, és az adott *ServiceType* tartozó szolgáltatások replikáit tárolja.
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
A **ServiceTypeDisableFailureThreshold** meghatározza a hibák (aktiválás, letöltés, CodePackage hibák) számát, amely után a ServiceType ütemezve van a blocklisting. Az első aktiválási hiba, a letöltési hiba vagy a CodePackage összeomlása a ServiceType blocklisting fogja ütemezni. A **ServiceTypeDisableGraceInterval** konfiguráció határozza meg azt a türelmi intervallumot, amely után a ServiceType blocklisted van megjelölve a csomóponton. Noha Mindez történik, az aktiválás, a letöltés és a CodePackage-újraindítás párhuzamosan próbálkozik. Az újrapróbálkozás például azt jelenti, hogy a CodePackage az összeomlás után újraindul, vagy a Service Fabric megkísérli a csomagok újbóli letöltését.

Ha a ServiceType értéke blocklisted, a rendszer "" System. hosting "hibát jelzett a következő tulajdonságnál:" ServiceTypeRegistration: ServiceType ". A ServiceType le lett tiltva a csomóponton. "

Ha a következők valamelyike történik, a ServiceType ismét engedélyezve lesz a csomóponton:
- Az aktiválás sikeres vagy eléri a **ActivationMaxFailureCount** újrapróbálkozásait.
- A letöltés sikeres, vagy a **DeploymentMaxFailureCount** újrapróbálkozások sikertelenek lesznek.
- Az összeomlást okozó CodePackage elindul, és sikeresen regisztrálja a ServiceType.

A **ActivationMaxFailureCount** és a **DeploymentMaxFailureCount** a próbálkozások maximális száma, Service Fabric az alkalmazás aktiválását vagy letöltését végzi egy csomóponton, amely után Service Fabric engedélyezi a ServiceType aktiválását. Ez azt eredményezi, hogy a szolgáltatás egy másik lehetőség az aktiváláshoz, ami sikeres lehet, ami a probléma automatikus gyógyulását eredményezi. Egy replika elhelyezésével és aktiválásával aktivált új aktiválási/letöltési művelet újból elindíthatja a ServiceType blocklisting, vagy sikeres lehet.

> [!NOTE]
> Ha a CodePackage, amely nem regisztrálja a ServiceType, a rendszer összeomlik, nem befolyásolja a ServiceType. Csak a replika-összeomlást üzemeltető CodePackage befolyásolja a ServiceType.
>

### <a name="codepackage-crash"></a>CodePackage összeomlása
Ha egy CodePackage összeomlik, Service Fabric egy leállítási használatával indítja el újra. A leállítási független attól, hogy a programkód regisztrálta-e a Service Fabric futtatókörnyezettel rendelkező típust.

A leállítási értéke min (RetryTime, **ActivationMaxRetryInterval**), a leállítási értéke pedig állandó, lineáris vagy exponenciális értékben növekszik a **ActivationRetryBackoffExponentiationBase** konfigurációs beállítás alapján.

- Állandó: if **ActivationRetryBackoffExponentiationBase** = = 0, RetryTime = **ActivationRetryBackoffInterval**;
- Lineáris: Ha  **ActivationRetryBackoffExponentiationBase** = = 0, akkor a RetryTime = ContinuousFailureCount * **ActivationRetryBackoffInterval** , ahol a ContinousFailureCount az a szám, ahányszor egy CodePackage összeomlik vagy nem sikerül aktiválni.
- Exponenciális: RetryTime = (**ActivationRetryBackoffInterval** másodpercben) * (**ActivationRetryBackoffExponentiationBase** ^ ContinuousFailureCount);
    
A viselkedést az értékek módosításával szabályozhatja. Ha például több gyors újraindítási kísérletre van szüksége, a **ActivationRetryBackoffExponentiationBase** 0 értékre és **ActivationRetryBackoffInterval** értékre állításával is használhatja a lineáris értéket. Ha ezekkel a beállításokkal összeomlott egy CodePackage, a kezdési időköz 10 másodperc után fog megjelenni. Ha a csomag továbbra is összeomlott, a leállítási 20, 30, 40 másodpercig és így tovább, egészen addig, amíg a CodePackage-aktiválás sikeres vagy a kód csomag inaktiválása megtörtént. 
    
A maximális idő Service Fabric a biztonsági mentést (vár) a **ActivationMaxRetryInterval** által a meghibásodást követően.
    
Ha a CodePackage összeomlik, és biztonsági mentést készít, a Service Fabric **CodePackageContinuousExitFailureResetInterval** kell maradnia, hogy az állapota Kifogástalan legyen, és ekkor a rendszer felülírja az előző hiba állapotáról szóló jelentést az ok és a ContinousFailureCount alaphelyzetbe állítása után.

### <a name="codepackage-not-registering-servicetype"></a>A CodePackage nem regisztrálja a ServiceType
Ha egy CodePackage életben marad, és várhatóan regisztrál egy ServiceType az Egyesült Államokban, de nem, Service Fabric figyelmeztetési állapotjelentés fog eredményezni a **ServiceTypeRegistrationTimeout** után, mondván, hogy a ServiceType nincs regisztrálva a várt időtartamon belül.

### <a name="activation-failure"></a>Aktiválási hiba
A Service Fabric mindig lineáris visszalépést használ (ugyanaz, mint a CodePackage Crash), amikor hibát talál az aktiválás során. Ez azt jelenti, hogy az aktiválási művelet a következő után fog megjelenni: (0 + 10 + 20 + 30 + 40) = 100 másodperc (az első újrapróbálkozás azonnali). Az aktiválás után a rendszer nem próbálkozik újra.
    
Az aktiválási leállítási maximálisan **ActivationMaxRetryInterval** , és újrapróbálkozhat a **ActivationMaxFailureCount**.

### <a name="download-failure"></a>Letöltési hiba
Service Fabric mindig lineáris biztonsági mentést használ, ha a letöltés során hiba lép fel. Ez azt jelenti, hogy az aktiválási művelet a következő után fog megjelenni: (0 + 10 + 20 + 30 + 40) = 100 másodperc (az első újrapróbálkozás azonnali). Ezt követően a letöltés nem próbálkozik újra. A letöltés lineáris visszakapcsolási értéke a ContinuousFailureCount **_DeploymentRetryBackoffInterval_* , és a maximálisan visszatérhet a **DeploymentMaxRetryInterval**. Az aktiválásokhoz hasonlóan a letöltési művelet is újra próbálkozik a **ActivationMaxFailureCount**.

> [!NOTE]
> Mielőtt módosítaná ezeket a beállításokat, néhány példát figyelembe kell vennie.

* Ha a CodePackage összeomlik, és a biztonsági mentés ki van kapcsolva, a ServiceType le lesz tiltva. Ha azonban az aktiválási konfiguráció egy gyors újraindítással rendelkezik, akkor a CodePackage néhány alkalommal elérheti, mielőtt a ServiceType ténylegesen blocklisted. Például: tegyük fel, hogy a CodePackage megérkezik, regisztrálja Service Fabric, majd összeomlik a ServiceType. Ebben az esetben az **ServiceTypeDisableGraceInterval** időszak megszakadása esetén a rendszer megszakítja a regisztráció típusát. Ez pedig megismételhető, amíg a CodePackage a  **ServiceTypeDisableGraceInterval** -nál nagyobb értékre vált, majd a ServiceType a csomóponton blocklisted lesz. A ServiceType blocklisted maytake hosszabb ideig tart.

* Aktiválások esetén, amikor Service Fabric rendszernek replikát kell elhelyeznie egy csomóponton, a RA (ReconfigurationAgent) megkérdezi az alrendszert, hogy aktiválja az alkalmazást, és 15 másodpercenként újrapróbálkozik az aktiválási kéréssel (a **RAPMessageRetryInterval** konfigurációs beállítás szabályozza). Service Fabric tudnia kell, hogy a ServiceType blocklisted lett, az üzemeltetési aktiválási műveletnek hosszabb ideig kell élnie, mint az újrapróbálkozási időköz és a **ServiceTypeDisableGraceInterval**. Példa: tegyük fel, hogy a fürt **ActivationMaxFailureCount** 5 értékre van állítva, és a **ActivationRetryBackoffInterval** értéke 1 másodperc. Ez azt jelenti, hogy az aktiválási művelet a következő után fog megjelenni: (0 + 1 + 2 + 3 + 4) = 10 másodperc (emlékeztetve arra, hogy az első újrapróbálkozás azonnali), majd azt követően, hogy a gazdagép újrapróbálkozik. Ebben az esetben az aktiválási művelet befejezve lesz, és 15 másodperc elteltével nem próbálkozik újra. Ez azért történik, mert a Service Fabric 15 másodpercen belül elfogyott az összes engedélyezett újrapróbálkozás. Így a ReconfigurationAgent minden újrapróbálkozása új aktiválási műveletet hoz létre az alrendszer üzemeltetése során, és a minta megismétli. Ennek eredményeképpen a ServiceType soha nem lesz blocklisted a csomóponton. Mivel a ServiceType nem kap blocklisted a csomóponton, a replikát a rendszer nem helyezi át és próbálkozik egy másik csomóponton.
> 

## <a name="deactivation"></a>Inaktiválása

Ha egy csomóponton aktiválják a szervizcsomagot, az inaktiválást nyomon követik. 

Az Inaktiválás két módon működik:

- Időnként: minden **DeactivationScanInterval** ellenőrzi azokat a ServicePackages, amelyek soha nem üzemeltettek replikát, és az inaktiválásra jelöltként jelöli meg őket.
- ReplicaClose: Ha egy replika be van zárva, a Deactivator beolvas egy DecrementUsageCount. Ha a Count 0 értéket mutat, az azt jelenti, hogy a szervizcsomag nem üzemeltet replikát, ezért az inaktiválásra jelölt.

 A [kizárólagos/megosztott][a2]aktiválási mód alapján a Deaktiválásra jelölt pályázók a SharedMode **DeactivationGraceInterval** után és a ExclusiveMode **ExclusiveModeDeactivationGraceInterval** után lesznek ütemezve. Ha ebben az időszakban egy új replika elhelyezése történik, a rendszer megszakítja az inaktiválást.

### <a name="periodically"></a>Rendszeresen
Beszéljünk néhány példát az időszakos inaktiválásra:

1. példa: tegyük fel, hogy a Deactivator a következő időpontban végez vizsgálatot: T (**DeactivationScanInterval**). A következő vizsgálat a következő időpontban fog megjelenni: 2T. Tegyük fel, hogy a szervizcsomag aktiválása a T + 1 időpontban történt. Ez a szervizcsomag nem üzemeltet replikát, ezért inaktiválva kell lennie. Ahhoz, hogy a szervizcsomagok inaktiválásra legyenek kijelöltként, a rendszernek nem kell replikát használnia a atleast T-időre vonatkozóan. Ez azt jelenti, hogy a rendszer a 2T + 1 címen inaktiválásra jogosult. Így a 2T-vizsgálat nem találja ezt a szervizcsomagot inaktiválásra jelöltként. A következő inaktiválási ciklus 3T ezt a szervizcsomagot inaktiválásra fogja ütemezni, mert most már nem volt replika állapotban a T ideje.  

2. példa: tegyük fel, hogy a rendszer a T-1 időpontban aktiválja a szervizcsomagot, és a deélénkítő ellenőrzi a T-T. A szervizcsomag nem üzemeltet replikát. Ezután a következő vizsgálat 2T ez a szervizcsomag inaktiválásra jelöltként lesz megtalálva, ezért az inaktiválásra lesz ütemezve.  

3. példa: tegyük fel, hogy egy szervizcsomag a T-1-ben aktiválva lesz, és a Deactivator a T-T vizsgálja. A szervizcsomag még nem üzemeltet replikát. Most a T + 1 replika lesz elhelyezve, azaz Az üzemeltetés egy IncrementUsageCount kap, ami azt jelenti, hogy létrejön egy replika. Most, 2T ez a szervizcsomag nem lesz ütemezve inaktiválásra. Mivel a rendszer replikát tartalmaz, az Inaktiválás az alább ismertetett ReplicaClose logika felé mozdul.

4. példa: tegyük fel, hogy a szervizcsomag nagy méretű, azaz 10 GB, és a csomóponton egy kis időt vehet igénybe. Az alkalmazás aktiválása után a Deactivator nyomon követi az életciklusát. Ha a **DeactivationScanInterval** -konfiguráció egy kis értékre van állítva, akkor előfordulhat, hogy olyan problémákba ütközik, amelyekben a szervizcsomag nem kap időt az aktiválásra a csomóponton, mert az idő múlásával a letöltés már megtörtént. A probléma megoldásához [előre letöltheti a szervizcsomagot a csomóponton][p1] , vagy növelheti a **DeactivationScanInterval**. 

> [!NOTE]
> A szervizcsomagok bárhonnan inaktiválva lehetnek (**DeactivationScanInterval** – 2 **_DeactivationScanInterval_*) + **DeactivationGraceInterval** /** ExclusiveModeDeactivationGraceInterval * *. 
>

### <a name="replica-close"></a>Replika lezárása:
Az Inaktiválás a szervizcsomagok által birtokolt replikák számát tartja fenn. Ha a szervizcsomag egy replikát tárol, és a replika le/le van zárva, a gazdagép DecrementUsageCount kap. Egy replika megnyitásakor a gazdagép IncrementUsageCount kap. A csökkentés azt jelenti, hogy a szervizcsomag jelenleg egy kevesebb replikát üzemeltet, és ha a számláló 0-ra csökken, akkor a szervizcsomag inaktiválásra van ütemezve, és az az idő, amely után az inaktiválva lesz, a **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**. 

Például: tegyük fel, hogy a T és a (z) 2t + X (**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**) esetében az inaktiválásra van ütemezve. Ha ez idő alatt a gazdagép egy IncrementUsage, ami azt jelenti, hogy létrejön egy replika, akkor a rendszer megszakítja az inaktiválást.

> [!NOTE]
> Mit jelentenek ezek a konfigurációs beállítások?
**DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: az az idő, amelyet a szervizcsomagok egy másik replikát üzemeltetnek a replikák futtatása után. 
**DeactivationScanInterval**: az a legkisebb idő, amelyet a szervizcsomagnak a replika üzemeltetéséhez kell adni, ha soha nem üzemeltetett replikát, azaz Ha nincs használatban.
>

### <a name="ctrlc"></a>Ctrl+C
Ha a szervizcsomag átadja a **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval** , és továbbra sem replikát üzemeltet, az Inaktiválás nem visszavonhatatlan. A CodePackage egy CTRL + C kezelőt állítanak ki, amely azt jelenti, hogy most az inaktiválási folyamatnak végig kell haladnia, hogy leállítsa a folyamatot. Ebben az időszakban, ha az ugyanarra a szervizcsomagra vonatkozó új replika megpróbál bekerülni, a művelet sikertelen lesz, mert nem lehet áttérni az inaktiválásról az aktiválásra.

## <a name="cluster-configs"></a>Fürt konfigurációi

Az aktiválási/decativation befolyásoló alapértékekkel rendelkező konfigurációk.

### <a name="servicetype"></a>ServiceType
**ServiceTypeDisableFailureThreshold**: alapértelmezett 1. A hibák számának küszöbértéke, amely után az FM (FailoverManager) értesítést kap, hogy letiltsa a szolgáltatás típusát a csomóponton, és egy másik csomópontot próbál meg elhelyezésre.
**ServiceTypeDisableGraceInterval**: alapértelmezés szerint 30 mp. Az az időtartam, amely után a szolgáltatástípus le lehet tiltani.
**ServiceTypeRegistrationTimeout**: alapértelmezett 300 másodperc. A ServiceType Service Fabric való regisztrálásának időtúllépése.

### <a name="activation"></a>Aktiválás
**ActivationRetryBackoffInterval**: alapértelmezett 10 mp. Leállítási időköz minden aktiválási hiba esetén.
**ActivationMaxFailureCount**: alapértelmezett 20. A maximális darabszám, amely után a rendszer újrapróbálkozik a sikertelen aktiválással. 
**ActivationRetryBackoffExponentiationBase**: alapértelmezett 1,5.
**ActivationMaxRetryInterval**: alapértelmezett 3600 másodperc. Max. a hibák elhárítása az aktiváláshoz.
**CodePackageContinuousExitFailureResetInterval**: alapértelmezett 300 másodperc. A CodePackage folyamatos kilépési hibáinak számának alaphelyzetbe állítására szolgáló időkorlát.

### <a name="download"></a>Letöltés
**DeploymentRetryBackoffInterval**: alapértelmezett 10. Az üzembe helyezési hiba időkorlátja.
**DeploymentMaxRetryInterval**: alapértelmezett 3600 másodperc. A telepítéshez szükséges maximális biztonsági mentés a hibák esetén.
**DeploymentMaxFailureCount**: alapértelmezett 20. Az alkalmazás központi telepítése újra próbálkozik a DeploymentMaxFailureCount, mielőtt az alkalmazás telepítése a csomóponton meghiúsul.

### <a name="deactivation"></a>Inaktiválása
**DeactivationScanInterval**: alapértelmezett 600 másodperc. Egy replika üzemeltetéséhez szükséges minimális idő a szervizcsomag tárolására, ha még soha nem üzemeltetett replikát, azaz Ha nincs használatban.
**DeactivationGraceInterval**: alapértelmezett 60 másodperc. A szervizcsomagnak egy másik replika futtatására adott idő, ha **megosztott** négyszínes modell esetén replikát üzemeltetett.
**ExclusiveModeDeactivationGraceInterval**: alapértelmezett 1 MP. Az az idő, amelyet a szervizcsomagnak egy másik replikát kell üzemeltetni, ha a **kizárólagos** folyamatmodell esetében bármilyen replikát futtatott.

## <a name="next-steps"></a>További lépések
[Alkalmazás becsomagolása][a3] és üzembe helyezése készen áll a telepítésre.

[Alkalmazások telepítése és eltávolítása][a4]. Ez a cikk azt ismerteti, hogyan használható a PowerShell az alkalmazások példányainak kezeléséhez.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
