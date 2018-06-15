---
title: Azure Batch Rendering szolgáltatás – felhőméretű renderelés | Microsoft Docs
description: Renderelési feladatok végezhetők Azure-alapú virtuális gépeken, közvetlenül a Maya szoftverből és használatalapú fizetéssel.
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: hero-article
ms.date: 05/10/2018
ms.author: danlep
ms.openlocfilehash: df1b2da7628e6c3f9f4bcbb02a936c33aad49698
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076971"
---
# <a name="get-started-with-the-batch-rendering-service"></a>Ismerkedés a Batch Rendering szolgáltatással

Az Azure Batch Rendering szolgáltatás felhőméretű renderelési képességeket nyújt használatalapú fizetéssel. A Batch Rendering szolgáltatás elvégzi a feladatok ütemezését és sorba rendezését, a hibák és az újrapróbálkozások kezelését és a renderelési feladatokkal kapcsolatos automatikus skálázást. A Batch Rendering szolgáltatás támogatja renderelési alkalmazások, például az [Autodesk Maya](https://www.autodesk.com/products/maya/overview), a [3ds Max](https://www.autodesk.com/products/3ds-max/overview), az [Arnold](https://www.autodesk.com/products/arnold/overview) és a [V-Ray](https://www.chaosgroup.com/vray/maya) használatát. A Maya 2017-hez készült Batch beépülő modullal könnyedén elindíthat egy renderelési feladatot az Azure-on, rögtön a számítógépről.

A Maya és a 3ds Max szoftverekkel a [BatchLabs](https://github.com/Azure/BatchLabs) asztali alkalmazás vagy a [Batch Templates parancssori felület](batch-cli-templates.md) használatával futtathat feladatokat. Az Azure Batch parancssori felületén kódírás nélkül futtathat Batch-feladatokat. Helyette sablonfájlok használatával hozhat létre Batch-készleteket, -feladatokat és -tevékenységeket. További információkért tekintse át [az Azure Batch parancssori felületi sablonjainak és fájlátviteli funkciójának használatával](batch-cli-templates.md) foglalkozó témakört.


## <a name="supported-applications"></a>Támogatott alkalmazások köre

A Batch Rendering szolgáltatás jelenleg a következő alkalmazások használatát támogatja:

CentOS 7 renderelési csomópontok esetében:
- Autodesk Maya I/O 2017 5. frissítés (201708032230-s verzió)
- Autodesk Maya I/O 2018 2. frissítés 201711281015-ös verzió
- Autodesk Arnold for Maya 2017 (Arnold verzió: 5.0.1.1) MtoA-2.0.1.1-2017
- Autodesk Arnold for Maya 2018 (Arnold verzió: 5.0.1.4) MtoA-2.1.0.3-2018
- Chaos Group V-Ray for Maya 2017 (3.60.04-es verzió) 
- Chaos Group V-Ray for Maya 2018 (3.60.04-es verzió) 
- Blender (2.68)

Windows Server 2016 renderelési csomópontok esetén:
- Autodesk Maya I/O 2017 5. frissítés (17.4.5459-es verzió) 
- Autodesk Maya I/O 2018 2. frissítés (18.2.0.6476-os verzió) 
- Autodesk 3ds Max I/O 2018 4. frissítés (20.4.0.4254-es verzió) 
- Autodesk Arnold for Maya (Arnold verzió: 5.0.1.1) MtoA-2.0.1.1-2017
- Autodesk Arnold for Maya (Arnold verzió: 5.0.1.4) MtoA-2.0.2.3-2018
- Autodesk Arnold for 3ds Max (Arnold verzió: 5.0.2.4) (1.2.926-os verzió) 
- Chaos Group V-Ray for Maya (3.52.03-as verzió) 
- Chaos Group V-Ray for 3ds Max (3.60.02-es verzió)
- Blender (2.79)


## <a name="prerequisites"></a>Előfeltételek

A Batch szolgáltatás használatához a következőkre van szükség:

- [Egy Azure-fiók](https://azure.microsoft.com/free/).
- **Egy Azure Batch-fiók.** Egy Batch-fiók az Azure Portalon történő létrehozásával kapcsolatos útmutatásért lásd a [Batch-fiókok az Azure Portalon történő létrehozását](batch-account-create-portal.md) ismertető cikket.
- **Egy Azure Storage-fiók.** A renderelési feladathoz használt adategységeket általában az Azure Storage tárolja. A Batch-fiók beállításakor automatikusan létrehozható egy tárfiók. Vagy használhat egy létező tárfiókot is. A Batch szolgáltatásban elérhető tárfiók-lehetőségekről további információt [a Batch funkcióinak áttekintésében](batch-api-basics.md#azure-storage-account) talál.
- **Környezeti változók.** Ha a megoldása környezeti változókat módosít, győződjön meg arról, hogy az `AZ_BATCH_ACCOUNT_URL` és az `AZ_BATCH_SOFTWARE_ENTITLEMENT_TOKEN` értékek nem változnak, és jelen vannak a fenti licencelt alkalmazások hívásakor. Ellenkező esetben valószínűleg szoftveraktivációs problémába fog ütközni.
- **BatchLabs** (nem kötelező). A [BatchLabs](https://azure.github.io/BatchLabs) egy ingyenes, számos funkcióval ellátott, különálló ügyféleszköz Azure Batch-alkalmazások létrehozásához, hibakereséséhez és monitorozásához. Bár a renderelési szolgáltatás használata nem kötelező, hasznos lehetőség a Batch-megoldások fejlesztéséhez és hibakereséséhez.

A Maya alkalmazáshoz készült Batch beépülő modul használatához a következőkre van szükség:

- [Autodesk Maya 2017](https://www.autodesk.com/products/maya/overview).
- Egy támogatott megjelenítő, például az Arnold for Maya vagy a V-Ray for Maya.

## <a name="basic-batch-concepts"></a>Alapszintű Batch-fogalmak

Mielőtt elkezdené használni a Batch Rendering szolgáltatást, nem árt, ha tisztában van néhány Batch-fogalommal, mint például a számítási csomópontok, a készletek és a feladatok jelentésével. Az Azure Batch szolgáltatással kapcsolatos általános információkért lásd [a párhuzamos számítási feladatok Batch használatával történő futtatását](batch-technical-overview.md) ismertető cikket.

### <a name="pools"></a>Készletek

A Batch egy platformszolgáltatás nagy számítási igényű munkák, pl. renderelés egy **számítási csomópontokból** álló **készleten** történő futtatásához. Egy készleten belül minden számítási csomópont egy Azure-beli virtuális gép (VM), amely Linux vagy Windows rendszert futtat. 

A Batch készleteivel és számítási csomópontjaival kapcsolatos további információkért lásd a [nagy léptékű párhuzamos számítási megoldások Batch segítségével történő fejlesztését](batch-api-basics.md) ismertető cikk [Készlet](batch-api-basics.md#pool) és [Számítási csomópont](batch-api-basics.md#compute-node) elnevezésű szakaszát.

### <a name="jobs"></a>Feladatok

A Batch-**feladatok** olyan tevékenységek gyűjteményei, amelyek egy készlet számítási csomópontjain futnak. Egy renderelési feladat elküldésekor a Batch tevékenységekre osztja a feladatot, és a futtatáshoz szétosztja a tevékenységeket a készlet számítási csomópontjai között.

Az [Azure Portalon](https://ms.portal.azure.com/) a feladatok monitorozásához és a sikertelen feladatok diagnosztizálásához letölthet alkalmazásnaplókat, és távolról kapcsolódhat az egyes virtuális gépekhez az RDP vagy az SSH használatával. Emellett felügyeleti, monitorozási és hibaelhárítási feladatokat is végezhet a [BatchLabs eszközzel](https://azure.github.io/BatchLabs).

A Batch-feladatokkal kapcsolatos további információkért lásd a [nagy léptékű párhuzamos számítási megoldások Batch segítségével történő fejlesztését](batch-api-basics.md) ismertető cikk [Feladat](batch-api-basics.md#job) elnevezésű szakaszát.

## <a name="options-for-provisioning-required-applications"></a>Beállítások a szükséges alkalmazások üzembe helyezéséhez

Adott feladatok rendereléséhez több alkalmazásra is szükség lehet, például a Mayára és az Arnoldra vagy a 3ds Maxra és a V-Rayre, valamint szükség szerint egyéb külső beépülő modulokra is. Egyes ügyfelek ezen alkalmazások adott verzióinak használatát is megkövetelhetik. Ezért több módszer is rendelkezésre áll a szükséges alkalmazások és szoftverek üzembe helyezésére:

### <a name="pre-configured-vm-images"></a>Előre konfigurált virtuálisgép-rendszerképek

Az Azure minden egyes Windows- és Linux-rendszerképre előre és használatra készen telepíti a Maya, a 3ds Max, az Arnold és a V-Ray egy-egy verzióját. A rendszerképeket az [Azure Portalon](https://portal.azure.com), a Maya beépülő modulban vagy a [BatchLabs](https://azure.github.io/BatchLabs) alkalmazásban választhatja ki, amikor létrehoz egy készletet.

Az Azure Portalon és a BatchLabsben a következők szerint telepítheti az előre telepített alkalmazásokkal rendelkező virtuálisgép-rendszerképeket: a Batch-fiók Készletek szakaszában válassza az **Új** lehetőséget, majd a **Készlet hozzáadása** területen a **Grafikus és megjelenítési szoftverek (Linux/Windows)** lehetőséget a **Lemezkép típusa** legördülő listából:

![Batch-fiók lemezképtípusának kiválasztása](./media/batch-rendering-service/add-pool.png)

Görgessen le, és a **Grafikus és megjelenítési szoftverek licencelése** területen kattintson a **Szoftver és díjszabás kiválasztása** elemre. Válasszon ki legalább egy szoftverlicencet:

![Grafikus és megjelenítési szoftverek licencének kiválasztása a készlethez](./media/batch-rendering-service/graphics-licensing.png)

A megadott licencverziók megegyeznek a fenti „Támogatott alkalmazások köre” szakaszban leírt verziószámokkal.

### <a name="custom-images"></a>Egyéni rendszerképek

Az Azure Batch egyéni rendszerképek megadását is lehetővé teszi, így pontosan azokkal az alkalmazásokkal és verziókkal konfigurálhatja a virtuális gépet, amelyekre szüksége van. További információkért lásd a [virtuálisgép-készletek egyéni rendszerkép használatával történő létrehozásával](https://docs.microsoft.com/azure/batch/batch-custom-images) foglalkozó témakört. Vegye figyelembe, hogy az Autodesk és a Chaos Group módosították az Arnold és a V-Ray szoftvert, így azok a mi licencszolgáltatásunkkal érvényesíthetők. Emiatt ezeknek az alkalmazásoknak mindenképp olyan verzióját kell használnia, amely rendelkezik ilyen támogatással, máskülönben a használatalapú licencelés nem fog működni. A Maya és a 3ds Max esetében nincs szükség ilyen licencérvényesítésre, mivel ezeknek az aktuális közzétett verziói nem igényelnek licenckiszolgálót, ha távfelügyeleti módban (kötegelt/parancssori módban) futnak. Amennyiben nem tudja, hogyan használhatja ezt a lehetőséget, forduljon az Azure ügyfélszolgálatához.

## <a name="options-for-submitting-a-render-job"></a>Renderelési feladatok elküldésének különböző lehetőségei

A használt 3D-alkalmazástól függően különféle lehetőségek állnak rendelkezésre, hogy be tudja küldeni a renderelési feladatokat a szolgáltatásba:

### <a name="maya"></a>Maya

A Mayával a következőket használhatja:

- [A Maya alkalmazáshoz készült Batch beépülő modult](https://docs.microsoft.com/azure/batch/batch-rendering-service#use-the-batch-plug-in-for-maya-to-submit-a-render-job)
- A [BatchLabs](https://azure.github.io/BatchLabs) asztali alkalmazást
- A [Batch-sablonok parancssori felületét](batch-cli-templates.md)

### <a name="3ds-max"></a>3ds Max

A 3ds Maxszel a következőket használhatja:

- A [BatchLabs](https://azure.github.io/BatchLabs) asztali alkalmazást (a 3ds Max BatchLabs-sablonok használatára vonatkozó útmutatásért lásd a [BatchLabs-adatokat](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) összefoglaló témakört)
- A [Batch-sablonok parancssori felületét](batch-cli-templates.md)

A 3ds Max Batch Labs-sablonok segítségével VRay- és Arnold-jeleneteket renderelhet az Azure Batch renderelési szolgáltatásával. A VRay- és Arnold-sablonnak két változata létezik, az egyik a standard jelenetekhez, a másik pedig az olyan összetett jelenetekhez, amelyekhez egy 3ds Max-útvonalfájl szükséges az objektumokhoz és a textúrákhoz (.mxp fájl). A 3ds Max Batch Labs-sablonokkal kapcsolatos további információkért lásd a [BatchLabs-adatok](https://github.com/Azure/BatchLabs-data/tree/master/ncj/3dsmax) adattárat a GitHubon.

Emellett használhatja a [Batch Python SDK-t](https://docs.microsoft.com/azure/batch/batch-python-tutorial) is, hogy a renderelési szolgáltatást integrálni tudja a meglévő folyamattal.


## <a name="use-the-batch-plug-in-for-maya-to-submit-a-render-job"></a>Renderelési feladat elküldése a Maya alkalmazáshoz készült Batch beépülő modul használatával

A Maya alkalmazáshoz készült Batch beépülő modul használatával közvetlenül a Mayából küldhet feladatokat a Batch Rendering szolgáltatásnak. A következő szakaszok a feladatnak a beépülő modulból történő konfigurálását, majd elküldését ismertetik. 

### <a name="load-the-batch-plug-in-for-maya"></a>A Maya alkalmazáshoz készült Batch beépülő modul betöltése

A Batch beépülő modul elérhető a [GitHubon](https://github.com/Azure/azure-batch-maya/releases). Csomagolja ki a fájlt egy szabadon választott könyvtárba. A beépülő modul közvetlenül betölthető az *azure_batch_maya* könyvtárból.

A beépülő modul betöltése a Maya alkalmazásban:

1. Indítsa el a Maya alkalmazást.
2. Nyissa meg a **Window** > **Settings/Preferences** > **Plug-in Manager** (Ablak/Beállítások/Beépülő modulok kezelése) elemet.
3. Kattintson a **Browse** (Tallózás) gombra.
4. Keresse meg, majd válassza ki az *azure_batch_maya/plug-in/AzureBatch.py* fájlt.

### <a name="authenticate-access-to-your-batch-and-storage-accounts"></a>A Batch- és Storage-fiókok hozzáférésének engedélyezése

A beépülő modul használatához hitelesítést kell végeznie az Azure Batch- és Azure Storage-fiókja kulcsaival. A fiókkulcsok beszerzéséhez tegye a következőket:

1. Jelenítse meg a beépülő modult a Mayában, és válassza a **Config** (Konfigurálás) lapot.
2. Lépjen az [Azure Portalra](https://portal.azure.com).
3. Válassza a bal oldali menüben a **Batch-fiókok** elemet. Ha szükséges, kattintson a **További szolgáltatások** elemre, majd állítsa be a _Batch_ szűrést.
4. Keresse ki a listából a kívánt Batch-fiókot.
5. Válassza a **Kulcsok** menüpontot a fiók neve, URL-címe és hozzáférési kulcsai megjelenítéséhez:
    - Illessze be a Batch-fiók URL-címét a **Service** (Szolgáltatás) mezőbe a Batch beépülő modulban.
    - Illessze be a fiók nevét a **Batch Account** (Batch-fiók) mezőbe.
    - Illessze be az elsődleges fiókkulcsot a **Batch Key** (Batch-kulcs) mezőbe.
7. Válassza a bal oldali menüben a Storage-fiókok lehetőséget. Ha szükséges, kattintson a **További szolgáltatások** elemre, majd állítsa be a _Storage_ szűrést.
8. Keresse ki a listából a kívánt Storage-fiókot.
9. Válassza a **Hozzáférési kulcsok** menüpontot a tárfiók neve és hozzáférési kulcsai megjelenítéséhez.
    - Illessze be a Storage-fiók nevét a **Storage Account** (Storage-fiók) mezőbe a Batch beépülő modulban.
    - Illessze be az elsődleges fiókkulcsot a **Storage Key** (Storage-kulcs) mezőbe.
10. Kattintson az **Authenticate** (Hitelesítés) gombra, hogy a beépülő modul hozzáférjen mindkét fiókhoz.

A sikeres hitelesítést követően a beépülő modul az állapotmezőben az **Authenticated** (Hitelesítve) állapotot jeleníti meg: 

![A Batch- és Storage-fiókok hitelesítése](./media/batch-rendering-service/authentication.png)

### <a name="configure-a-pool-for-a-render-job"></a>Egy készlet konfigurálása egy renderelési feladathoz

A Batch- és Storage-fiókok hitelesítését követően állítson be egy készletet a renderelési feladathoz. A beépülő modul a munkamenetek között megjegyzi a választott beállításokat. Ha egyszer már beállította a kívánt konfigurációját, többször nem kell módosítania, hacsak meg nem változik.

A következő szakaszok végigvezetik a **Submit** (Küldés) lapon elérhető lehetőségeken:

#### <a name="specify-a-new-or-existing-pool"></a>Új vagy létező készlet megadása

A renderelési feladathoz használni kívánt készlet megadásához válassza ki a **Submit** (Küldés) lapot. Ezen a lapon lehetősége van létrehozni egy új készletet, vagy kiválasztani egy meglévő készletet:

- **Automatikusan létrehozhat egy készletet a feladathoz** (ez az alapértelmezett beállítás). Ha ezt a lehetőséget választja, a Batch kizárólag a jelenlegi feladat számára hozza létre a készletet, amelyet automatikusan töröl is a renderelési feladat befejeztével. Ez a lehetőség a legjobb választás, ha egyetlen renderelési feladatot szeretne elvégezni.
- **Újrahasznosíthat egy meglévő állandó készletet.** Ha van egy meglévő, tétlen készlete, akkor a legördülő menüben kiválaszthatja, hogy az a készlet futtassa a renderelési feladatot. Egy meglévő állandó készlet újbóli felhasználásával megtakaríthatja a készlet létrehozásához szükséges időt.  
- **Létrehozhat egy új állandó készletet.** Ha ezt a lehetőséget választja, a rendszer létrehoz egy új készletet a feladat futtatásához. A feladat befejeztével nem törli a készletet, így az a későbbiekben más feladatokhoz is újból használható lesz. Válassza ezt a lehetőséget, ha rendszeresen kell végeznie renderelési feladatokat. A későbbi feladatok során válassza a **reuse an existing persistent pool** beállítást, ha az első feladathoz létrehozott állandó készletet szeretné felhasználni.

![Készlet, operációsrendszer-kép, virtuálisgép-méret és licenc megadása](./media/batch-rendering-service/submit.png)

Az Azure-beli virtuális gépekre vonatkozó díjszabásokkal kapcsolatos bővebb információért olvassa el a [linuxos díjszabásra](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#faq) és a [windowsos díjszabásra](https://azure.microsoft.com/pricing/details/virtual-machines/windows/#faq) vonatkozó gyakori kérdéseket.

#### <a name="specify-the-os-image-to-provision"></a>A létrehozáshoz használandó operációsrendszer-kép megadása

Az **Env** (Környezet) lapon adhatja meg, hogy milyen típusú operációsrendszer-képet szeretne használni a készletben található számítási csomópontok létrehozásához. A Batch jelenleg a következő rendszerképek használatát támogatja a renderelési feladatokhoz:

|Operációs rendszer  |Kép  |
|---------|---------|
|Linux     |Batch CentOS |
|Windows     |Batch Windows |

#### <a name="choose-a-vm-size"></a>Virtuális gép méretének kiválasztása

A virtuális gép méretét az **Env** (Környezet) lapon adhatja meg. Az elérhető virtuálisgép-méretekkel kapcsolatos bővebb információkért olvassa el az [Azure-beli linuxos virtuális gépek méreteit](../virtual-machines/linux/sizes.md), illetve az [Azure-beli windowsos virtuális gépek méreteit](../virtual-machines/windows/sizes.md) ismertető cikket. 

![A virtuális gép operációsrendszer-képének és méretének megadása az Env (Környezet) lapon](./media/batch-rendering-service/environment.png)

#### <a name="specify-licensing-options"></a>Licencelési beállítások megadása

A használni kívánt licenceket az **Env** (Környezet) lapon adhatja meg. A lehetőségek a következők:

- **Maya** – ez a beállítás alapértelmezés szerint engedélyezve van.
- **Arnold** – ez a beállítás akkor van engedélyezve, ha a rendszer észleli, hogy a Maya aktív renderelési motorja az Arnold.

 Ha a saját licencét használva kíván renderelést futtatni, konfigurálhatja a licencvégpontját úgy, hogy hozzáadja a táblázathoz a megfelelő környezeti változókat. Ebben az esetben ügyeljen arra, hogy az alapértelmezett licencelési beállítások jelölését törölje.

> [!IMPORTANT]
> A számlázás a licenchasználat alapján történik, amíg a készletben a virtuális gépek futnak, függetlenül attól, hogy a VM-ek éppen renderelést végeznek-e. A többletköltségek elkerüléséhez lépjen a **Pools** (Készletek) lapra, és méretezze át a készletet 0 csomópontra, ha éppen nem szeretne egy újabb renderelési feladatot futtatni. 
>
>

#### <a name="manage-persistent-pools"></a>Állandó készletek kezelése

A meglévő állandó készletek a **Pools** (Készletek) lapon kezelhetők. Ha kiválaszt a listából egy készletet, megjelenik a készlet aktuális állapota.

A **Pools** (Készletek) lapon törölheti is a készleteket, vagy átméretezheti a készletben található virtuális gépek számát. Átméretezhet egy készletet 0 csomópontra, így elkerülheti a számítási feladatok között felmerülő költségeket.

![Készletek megtekintése, átméretezése és törlése](./media/batch-rendering-service/pools.png)

### <a name="configure-a-render-job-for-submission"></a>Egy renderelési feladat konfigurálása küldéshez

Miután megadta a renderelési feladatot futtató készlet paramétereit, konfigurálja magát a feladatot is. 

#### <a name="specify-scene-parameters"></a>Jelenetparaméterek megadása

A Batch beépülő modul felismeri a Mayában éppen használt renderelési motort, és az annak megfelelő renderelési beállításokat jeleníti meg a **Submit** (Küldés) lapon. E beállítások közé tartozik a képkocka kezdete, a képkocka vége, a kimeneti előtag és a képkockaváltás. A jelenetfájl renderelési beállításai felülbírálhatók a beépülő modul különböző beállításaival. A beépülő modul beállításaiban elvégzett módosítások nem módosítják a jelenetfájl renderelési beállításait, így a módosítások feladatonkénti alapon elvégezhetők anélkül, hogy a jelenetfájlt újra fel kellene tölteni.

A beépülő modul figyelmeztet arra, ha nem támogatja a Maya alkalmazásban kiválasztott renderelési motort.

Ha betölt egy új jelenetet, miközben a beépülő modul meg van nyitva, kattintson a **Refresh** (Frissítés) gombra, hogy a beállítások biztosan frissüljenek.

#### <a name="resolve-asset-paths"></a>Adategységek elérési útjainak feloldása

A beépülő modul a betöltésekor megvizsgálja, hogy a jelenetfájl tartalmaz-e bármilyen külső fájlra mutató hivatkozást. Ezek a hivatkozások az **Assets** (Adategységek) lapon jelennek meg. Ha egy hivatkozott elérési utat nem lehet feloldani, a beépülő modul megkísérli megkeresni a fájlt néhány alapértelmezett helyen, beleértve a következőket is:

- a jelenetfájl helye; 
- az aktuális projekt _sourceimages_ könyvtára;
- az aktuális munkakönyvtár. 

Ha az adategység ezután sem található, egy figyelmeztető ikon jelenik meg mellette a listában:

![A hiányzó adategységek mellett egy figyelmeztető ikon jelenik meg](./media/batch-rendering-service/missing_assets.png)

Ha ismeri a feloldatlan fájlhivatkozás helyét, a figyelmeztető ikonra kattintva megadhatja a keresési útvonalat. A beépülő modul ekkor a keresési útvonalat használva megkísérli feloldani a hiányzó adategységeket. Tetszőleges számú további keresési útvonalat lehet megadni.

Ha egy hivatkozást a rendszer feloldott, akkor egy zöld jelzőlámpa ikon jelenik meg mellette:

![A feloldott adategységek mellett egy zöld jelzőlámpa ikon látható](./media/batch-rendering-service/found_assets.png)

Ha a jelenethez további fájlok is szükségesek, amelyeket a beépülő modul nem talált meg, akkor hozzáadhat további fájlokat és könyvtárakat. Használja az **Add Files** (Fájlok hozzáadása) és **Add Directory** (Könyvtár hozzáadása) gombot. Ha betölt egy új jelenetet, miközben a beépülő modul meg van nyitva, kattintson a **Refresh** (Frissítés) gombra, hogy a jelenet hivatkozásai biztosan frissüljenek.

#### <a name="upload-assets-to-an-asset-project"></a>Adategységek feltöltése egy adategység-projektbe

Egy renderelési feladat elküldésekor az **Assets** (Adategységek) lapon megjelenő hivatkozott fájlok automatikusan fel lesznek töltve az Azure Storage tárolóra egy adategység-projektként. Az adategység-fájlok a renderelési feladattól függetlenül is feltölthetők, az **Assets** (Adategységek) lap **Upload** (Feltöltés) gombjával. Az adategység-projekt nevét a **Project** (Projekt) mező adja meg. A projektek alapértelmezés szerint az aktuális Maya-projekt alapján kapnak nevet. Az adategység-fájlok feltöltésekor a helyi fájlszerkezetet a rendszer megőrzi. 

A feltöltött adategység-fájlokra bármennyi renderelési feladat hivatkozhat. Bármely feltöltött adategység elérhető bármely, az adategység-projektre hivatkozó feladat számára, függetlenül attól, hogy szerepelnek-e a jelenetben. Ha módosítania kell a következő feladat által hivatkozott adategység-projektet, módosítsa a projekt nevét az **Assets** (Adategységek) lap **Project** (Projekt) mezőjében. Ha vannak olyan hivatkozott fájlok, amelyeket ki szeretne hagyni a feltöltésből, a lista melletti zöld gombokkal törölje a jelölésüket.

#### <a name="submit-and-monitor-the-render-job"></a>A renderelési feladat küldése és monitorozása

Miután a renderelési feladatot konfigurálta a beépülő modulban, kattintson a **Submit** (Küldés) lapon a **Submit Job** (Feladat küldése) gombra a feladat Batch felé küldéséhez:

![A renderelési feladat küldése](./media/batch-rendering-service/submit_job.png)

A folyamatban lévő feladatok a beépülő modul **Jobs** (Feladatok) lapján keresztül monitorozhatók. Válasszon a listából egy feladatot az aktuális állapota megjelenítéséhez. Ezen a lapon megszakíthatja és törölheti is a feladatokat, valamint letöltheti a kimeneteket és a renderelési naplókat. 

A kimenetek letöltéséhez az **Outputs** (Kimenetek) mező módosításával adja meg a kívánt célkönyvtárt. A fogaskerék ikonra kattintva elindíthat egy háttérfolyamatot, amely figyeli a feladatokat, és menet közben letölti a kimeneteket: 

![Feladat állapotának megtekintése és kimenetek letöltése](./media/batch-rendering-service/jobs.png)

A Maya alkalmazást bezárhatja, ez ugyanis nem zavarja meg a letöltési folyamatot.

## <a name="next-steps"></a>További lépések

A Batch szolgáltatással kapcsolatos további információkért lásd [a párhuzamos számítási feladatok Batch használatával történő futtatását](batch-technical-overview.md) ismertető cikket.
