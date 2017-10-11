---
title: "Az Azure mikroszolgáltatások folyamatos integráció beállítása |} Microsoft Docs"
description: "Áttekintheti a folyamatos integrációt és telepítést a Service Fabric-alkalmazás beállítása a Visual Studio Team Services (VSTS) használatával."
services: service-fabric
documentationcenter: na
author: mthalman-msft
manager: timlt
editor: 
ms.assetid: 3e8c2290-9e7a-456a-9b2c-db44d1b3988d
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2016
ms.author: mthalman;mikhegn
ms.openlocfilehash: 76a1e013e824910c7a489e345b6563ae3951378f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="set-up-service-fabric-continuous-integration-and-deployment-with-visual-studio-team-services"></a>A Service Fabric folyamatos integrációt és telepítést a Visual Studio Team Services beállítása
Ez a cikk ismerteti a lépéseket folyamatos integrációt és üzembe helyezés az Azure Service Fabric-alkalmazás beállítása a Visual Studio Team Services (VSTS) használatával.

Ez a dokumentum az aktuális eljárás tükrözi, és várhatóan változnak az idők.

## <a name="prerequisites"></a>Előfeltételek
A kezdéshez, kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy rendelkezik-e a Team Services-fiókhoz való hozzáférés vagy [hozzon létre egyet](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) magát.
2. Győződjön meg arról, hogy rendelkezik-e a hozzáférést egy Team Services csapatprojekt vagy [hozzon létre egyet](https://www.visualstudio.com/docs/setup-admin/create-team-project) magát.
3. Győződjön meg arról, hogy rendelkezik-e a Service Fabric-fürt, amely az alkalmazás telepítéséhez, vagy hozzon létre egyet az a [Azure-portálon](service-fabric-cluster-creation-via-portal.md), egy [Azure Resource Manager sablon](service-fabric-cluster-creation-via-arm.md), vagy [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).
4. Győződjön meg arról, hogy már létrehozta a Service Fabric-alkalmazás (.sfproj) projektben. A projekt létrehozott vagy frissítik a Service Fabric SDK 2.1-es vagy újabb rendszer (a .sfproj fájlt tartalmaznia kell egy ProjectVersion tulajdonság értéke 1.1-es vagy magasabb) kell rendelkeznie.

> [!NOTE]
> Egyéni build ügynökök már nem szükségesek. Team Services üzemeltetett ügynökök most előtelepített Service Fabric-fürt felügyeleti szoftver lehetővé teszi a közvetlenül az adott ügynökök az alkalmazások központi telepítését.
> 
> 

## <a name="configure-and-share-your-source-files"></a>Konfigurálja és a forrás-fájlok megosztása
Először is meg szeretné, használja a központi telepítési folyamat, amely végrehajtja a belül Team Services közzétételi profilt előkészítése.  A közzétételi profil konfigurálni kell, hogy a célfürt korábban előkészítése után:

1. Válassza ki az alkalmazás projektben, használja a folyamatos integrációt munkafolyamat kívánt közzétételi profilt. Kövesse a [utasításokat közzététele](service-fabric-publish-app-remote-cluster.md) távoli fürtre alkalmazások közzétételét ismerteti. Nem ténylegesen kell, ha az alkalmazás közzétételére. Kattintson a **mentése** hivatkozás közzététele párbeszédpanelen dolgot megfelelően konfigurálása után.
2. Ha azt szeretné, hogy az alkalmazás az egyes központi telepítések Team Services belül előforduló frissítve lesz, a közzétételi profil történő frissítéshez konfigurálni szeretné. Az 1. lépésben használt azonos közzététele párbeszédpanelen győződjön meg arról, hogy a **az alkalmazás frissítése** jelölőnégyzet be van jelölve.  További információ [további frissítési beállítások konfigurálása](service-fabric-visualstudio-configure-upgrade.md). Kattintson a **mentése** mentse a beállításokat a közzétételi profil mutató hivatkozás.
3. Győződjön meg arról, hogy a módosítások a közzétételi profil mentett, és szakítsa meg a közzététele párbeszédpanelen.
4. Most ideje [megosztani az alkalmazás projekt forrásfájljait](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#vs) Team Services. Miután a forrásfájlok Team Services érhető el, most már továbbléphet buildek létrehozni a következő lépéssel. 

## <a name="create-a-build-definition"></a>A build definíció létrehozása
Team Services build definícióját egy munkafolyamatot, amelyik build ismertetett lépések egymás után végrehajtott áll ismerteti. A build-definíciót, amely létrehozásakor célja a Service Fabric-alkalmazáscsomagot, és más összetevők, az alkalmazás telepítéséhez használható. További tudnivalók Team Services [definíciók build](https://www.visualstudio.com/docs/build/define/create).

### <a name="create-a-definition-from-the-build-template"></a>A build sablonból definíció létrehozása
1. Nyissa meg a csoport projektet a Visual Studio Team Services.
2. Válassza ki a **Build** fülre.
3. Válassza ki a zöld  **+**  jelentkezzen be egy új build-definíció létrehozása.
4. A megnyíló párbeszédpanelen válasszon **Azure Service Fabric-alkalmazás** belül a **Build** sablon kategóriát.
5. Válassza ki **következő**.
6. Válassza ki a tárházhoz és a Service Fabric-alkalmazás társított ág.
7. Válassza ki a használni kívánt ügynök várólista. Bérelt ügynökök támogatottak.
8. Kattintson a **Létrehozás** gombra.
9. Mentse a build definícióját, és adjon meg egy nevet.
10. A következő bekezdés a build lépéseket a sablon által létrehozott leírása:

| Összeállítása lépés | Leírás |
| --- | --- |
| NuGet visszaállítása |Visszaállítja a NuGet-csomagok a megoldáshoz. |
| Megoldás \*.sln |A teljes megoldás alkot. |
| Megoldás \*.sfproj |A Service Fabric-alkalmazáscsomagot, amellyel az alkalmazás telepítéséhez hoz létre. Az alkalmazás csomag hely meg van adva a build összetevő directory belül. |
| Service Fabric-alkalmazás verziója frissítése |Az alkalmazáscsomag jegyzékfájlt frissítési támogatásának engedélyezéséhez szereplő verzióértékek frissíti. Tekintse meg a [feladat dokumentációs oldal](https://go.microsoft.com/fwlink/?LinkId=820529) további információt. |
| Fájlok másolása |Másolja a közzétételi profil és az alkalmazás paramétereit a build műtermékek központi telepítéshez használni. |
| Összetevő közzététele |A build összetevők közzéteszi. Lehetővé teszi, hogy a build összetevők felhasználásához kiadás definícióját. |

### <a name="verify-the-default-set-of-tasks"></a>Ellenőrizze a tevékenységek alapértelmezett készlete
1. Ellenőrizze a **megoldás** beviteli mezőt a **NuGet visszaállítási** és **megoldás** build lépéseket.  Alapértelmezés szerint ezek build lépések végrehajtása után a társított tárházban található összes megoldásfájlok.  Ha csak az egyik megoldás fájlokhoz való működésre build meghatározása, módosítania explicit módon az elérési utat a fájlhoz.
2. Ellenőrizze a **megoldás** beviteli mezőt a **alkalmazás becsomagolása** összeállítása lépés.  Alapértelmezés szerint a build lépés azt feltételezi, hogy csak egy Service Fabric-alkalmazás projekt (.sfproj) szerepel a tárházban.  Ha több fájlt a tárházban és cél csak az egyiket a build definíció, módosítania explicit módon az elérési utat a fájlhoz.  Ha azt szeretné, a csomag több alkalmazás projektet a tárházban, további létrehozásához szükséges **Visual Studio Build** a build definícióját, hogy minden érintett alkalmazás projektben lévő lépéseket.  Ezután is kell frissíteni a **MSBuild-argumentumok** mező az összes build lépéseket, hogy a szolgáltatáscsomag helyét nézve egyedi.
3. Ellenőrizze a definiált versioning viselkedését a **Service Fabric App verziók frissítése** összeállítása lépés.  Alapértelmezés szerint a build lépés hozzáfűzi a buildszám összes verzió értékének az alkalmazáscsomag jegyzékfájlt. Tekintse meg a [feladat dokumentációs oldal](https://go.microsoft.com/fwlink/?LinkId=820529) további információt. Ez akkor hasznos, támogatásához az alkalmazás frissítését, az egyes frissítés telepítése szükséges a korábbi központi telepítés eltérő verziójú értékeit. Ha még nem szándékozó alkalmazás frissítését használja a munkafolyamat, akkor fontolja meg a build lépés letiltása. Le kell tiltani Ha felülírja a meglévő Service Fabric-alkalmazás használható build létrehozásához. A telepítés sikertelen lesz, ha az alkalmazás által a build verziója nem egyezik meg a fürt alkalmazás verziója.
4. Ha a megoldás a .NET Core projektet tartalmaz, győződjön meg róla, hogy a build definícióját tartalmazza-e a build lépést, amely visszaállítja a függőségek:
   1. Válassza ki **összeállítása lépés hozzáadása...** .
   2. Keresse meg a **parancssori** feladatot a segédprogram lapon belül, és kattintson a Hozzáadás gombra.
   3. A tevékenység bemeneti mezők esetén a következő értékeket használja:
   4. Eszköz: dotnet
   5. Argumentumok: visszaállítása
   6. Húzza a feladatot úgy, hogy azonnal után a **NuGet visszaállítási** lépés.
5. A build definition végrehajtott módosítások mentése.

### <a name="try-it"></a>Kipróbálom
Válassza ki **várólista létrehozása** build manuális elindítása. Épít is eseményindítók leküldéses vagy be. Miután ellenőrizte, hogy sikeresen végrehajtja az, hogy a build, most már továbbléphet olyan kiadási definíciót, amely telepíti az alkalmazást egy fürt definiálásának.

## <a name="create-a-release-definition"></a>Egy kiadási definíció létrehozása
Team Services kiadás definíció olyan munkafolyamatot, amely a feladatok egymás után végrehajtott készlete áll ismerteti. A kiadási definíció létrehozása célja eltarthat egy alkalmazáscsomagot, és központilag telepítenie kell a fürthöz. Együttes használatuk esetén a build meghatározását és kiadás definition hajthat végre a teljes munkafolyamat a forrásfájlok egy a fürtben futó alkalmazás végződő kezdve. További tudnivalók Team Services [definíciók kiadási](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-definition-from-the-release-template"></a>A definíció létrehozása a kiadási sablon
1. Nyissa meg a projektet a Visual Studio Team Services.
2. Válassza ki a **kiadás** fülre.
3. Válassza ki a zöld  **+**  jelentkezzen be egy új kiadási definíció létrehozása, és válassza ki **létrehozás kiadás definition** a menüben.
4. A megnyíló párbeszédpanelen válassza ki a **Azure Service Fabric telepítési** belül a **telepítési** sablon kategóriát.
5. Válassza ki **következő**.
6. Válassza ki a kiadás a definícióját a forrásaként használni kívánt build definícióját.  A kiadási definition az összetevőket, amelyeket a kijelölt build definition hivatkozik.
7. Ellenőrizze a **folyamatos üzembe helyezés** jelölőnégyzetet, ha kíván Team Services automatikusan hozzon létre egy új kiadását, és a Service Fabric-alkalmazás központi telepítése, amikor build befejeződik.
8. Válassza ki a használni kívánt ügynök várólista. Bérelt ügynökök támogatottak.
9. Kattintson a **Létrehozás** gombra.
10. A definíció nevét szerkesztéséhez kattintson a ceruza ikonra az oldal tetején.
11. Válassza ki a fürtöt, amelyre az alkalmazást kell telepíteni, az a **Fürtkapcsolat** beviteli mezőt a feladat. A fürt kapcsolatot biztosít a szükséges információt, amely lehetővé teszi a szolgáltatástelepítési feladat kapcsolódik a fürthöz. Ha még nincs egy fürt kapcsolatban a fürt számára, jelölje be a **kezelése** vegyen fel egyet a mező melletti hivatkozásra. A megnyíló lapon hajtsa végre a következő lépéseket:
    1. Válassza ki **új szolgáltatási végpont** majd **Azure Service Fabric** a menüből.
    2. Válassza ki a végpont által megcélzott a fürt által használt hitelesítés típusa.
    3. Adja meg a nevét, a kapcsolat a **kapcsolatnév** mező.  Általában a fürt nevét használja.
    4. Adja meg az ügyfél kapcsolat végpont URL-címet a **a fürt végpontja** mező.  Példa: tcp://contoso.westus.cloudapp.azure.com:19000.
    5. Az Azure Active Directory hitelesítő adatok, adja meg a fürthöz való csatlakozáshoz használni kívánt hitelesítő adatokat a **felhasználónév** és **jelszó** mezőket.
    6. A tanúsítványalapú hitelesítéshez, az ügyfél tanúsítványfájlt a Base64 kódolás megadása az **ügyféltanúsítvány** mező.  Előugró súgójában talál információt a beszerzésére ezt az értéket a mezőhöz.  Ha a tanúsítvány jelszóval védett, adja meg a jelszót a **jelszó** mező.
    7. A módosítások megerősítése kattintva **OK**. Lépjen vissza a kiadási definícióját, után kattintson a frissítési ikonra a **Fürtkapcsolat** mezőben a végpont az előzőekben adott hozzá.
12. A kiadási definíció mentése.

> [!NOTE]
> Microsoft Accounts (például @hotmail.com vagy @outlook.com) Azure Active Directory-hitelesítés használata nem támogatott.
> 
> 

A létrehozott definition áll egy feladat típusú **Service Fabric-alkalmazás központi telepítésének**. Tekintse meg a [feladat dokumentációs oldal](https://go.microsoft.com/fwlink/?LinkId=820528) Ez a feladat további információt.

### <a name="verify-the-template-defaults"></a>A sablon alapértelmezett ellenőrzése
1. Ellenőrizze a **közzététele profil** beviteli mezőt a **Fabric-alkalmazás központi telepítése** feladat. Alapértelmezés szerint ez a mező szerepel a build összetevők Cloud.xml nevű közzétételi profilt hivatkozik. Ha szeretne hivatkozni különböző közzétételi profilt, vagy ha a build az összetevői a több alkalmazáscsomagot, frissítenie kell az elérési út megfelelően.
2. Ellenőrizze a **alkalmazáscsomag** beviteli mezőt a **Fabric-alkalmazás központi telepítése** feladat. Alapértelmezés szerint ez a mező alkalmazás csomag alapértelmezés szerint a build definícióban szereplő hivatkozik.  Ha módosította az alapértelmezett alkalmazás csomag elérési útját a build-definícióban, módosítania megfelelően itt az elérési út is.

### <a name="try-it"></a>Kipróbálom
Válassza ki **létrehozása kiadási** a a **kiadási** gomb menüre kattintva manuálisan létrehozhat egy kiadási. A megnyíló párbeszédpanelen válassza ki a build alapjául a kiadásban, és kattintson a kívánt **létrehozása**. Ha engedélyezte a folyamatos üzembe helyezés, kiadásokban fog jönnek létre automatikusan a kapcsolódó összeállítási definition befejezéséről build.

## <a name="next-steps"></a>Következő lépések
A Service Fabric-alkalmazások folyamatos integrációt kapcsolatos további tudnivalókért olvassa el a következő cikkeket:

* [Otthoni csoport szolgáltatások dokumentációja](https://www.visualstudio.com/docs/overview)
* [A Team Services management létrehozása](https://www.visualstudio.com/docs/build/overview)
* [Team Services szolgáltatásban](https://www.visualstudio.com/docs/release/overview)

