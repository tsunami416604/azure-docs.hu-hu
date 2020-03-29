---
title: Lemezkép-gyár futtatása az Azure DevOps szolgáltatásból az Azure DevTest Labsben
description: Ez a cikk ismerteti az Azure DevOps (korábbi képi stúdió team services) lemezképgyár futtatásához szükséges összes előkészületet.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bb67f765684c77ed5f8527226bef578e450579e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758682"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Rendszerkép-előállító futtatása az Azure DevOpsból
Ez a cikk ismerteti az Azure DevOps (korábbi képi stúdió team services) lemezképgyár futtatásához szükséges összes előkészületet.

> [!NOTE]
> Minden vezénylési motor működni fog! Az Azure DevOps nem kötelező. A lemezkép-gyár Azure PowerShell-parancsfájlok használatával fut, így manuálisan is futtatható a Windows Feladatütemező, más CI/CD-rendszerek és így tovább.

## <a name="create-a-lab-for-the-image-factory"></a>Labor létrehozása a képgyárszámára
A lemezképgyár beállításának első lépése egy labor létrehozása az Azure DevTest Labs-ben. Ez a labor a képgyári labor, ahol létrehoztuk a virtuális gépeket, és egyéni képeket menthetünk. Ez a labor a teljes képgyári folyamat része. Miután létrehozott egy tesztkörnyezetet, mentse a nevet, mivel később szüksége lesz rá.

## <a name="scripts-and-templates"></a>Parancsfájlok és sablonok
A következő lépés a rendszerképgyár elfogadása a csapat számára, hogy megértsék, mi áll rendelkezésre. A lemezkép-gyári parancsfájlok és sablonok nyilvánosan elérhetők a [DevTest Labs GitHub-tárházban.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory) Itt van egy vázlatot a darab:

- Image Factory. Ez a gyökérmappa.
    - Konfigurációs. A képgyár bemenetei
        - Aranyképek. Ez a mappa olyan JSON-fájlokat tartalmaz, amelyek az egyéni lemezképek definícióit jelölik.
        - Laborok.json. Az a fájl, ahol a csapatok regisztrálnak, hogy konkrét egyéni képeket kapjanak.
- Parancsfájlok. A képgyár motorja.

Az ebben a szakaszban található cikkek további részleteket tartalmaznak ezekről a parancsfájlokról és sablonokról.

## <a name="create-an-azure-devops-team-project"></a>Azure DevOps-csapatprojekt létrehozása
Az Azure DevOps lehetővé teszi a forráskód tárolását, és egy helyen futtathatja az Azure PowerShellt. Az ismétlődő futtatásokat ütemezheti a képek naprakészen tartásához. Vannak jó lehetőségek fakitermelés az eredmények diagnosztizálására bármilyen problémát.  Az Azure DevOps használata azonban nem követelmény, azonban bármilyen hámot/motort használhat, amely csatlakozhat az Azure-hoz, és futtathatja az Azure PowerShellt.

Ha egy meglévő DevOps-fiókkal vagy projekttel rendelkezik, amelyet helyette használni szeretne, hagyja ki ezt a lépést.

Első lépésekhez hozzon létre egy ingyenes fiókot az Azure DevOps-ban. Látogasson https://www.visualstudio.com/ el, és válassza **az Első lépések lehetőséget közvetlenül az** Azure **DevOps** (korábbi VSTS) alatt. Ki kell választania egy egyedi fióknevet, és biztosnak kell lennie arról, hogy a Git használatával kezeli a kódot. Miután létrehozta, mentse az URL-címet a csoportprojekthez. Itt van egy `https://<accountname>.visualstudio.com/MyFirstProject`minta URL: .

## <a name="check-in-the-image-factory-to-git"></a>Ellenőrizze a kép gyár Git
A PowerShell, a lemezképgyár összes sablonja és konfigurációja a [nyilvános DevTest Labs GitHub-tárházban](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)található. A leggyorsabb módja annak, hogy a kódot az új csapat projekt importálása tárház. Ez lekéri a teljes DevTest Labs tárház (így kap további docs, és minták).

1. Keresse fel az előző lépésben létrehozott Azure DevOps-projektet (az URL-cím **\//\<https: accountname>.visualstudio.com/MyFirstProject).**
2. Válassza **a Tárház importálása**lehetőséget.
3. Adja meg a DevTest Labs repo **klónjának URL-címét:** `https://github.com/Azure/azure-devtestlab`.
4. Kattintson az **Importálás** gombra.

    ![Git-tárhétimportálása](./media/set-up-devops-lab/import-git-repo.png)

Ha úgy dönt, hogy csak ellenőrizni, hogy pontosan mire van szükség (a kép gyári fájlokat), kövesse az [itt](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) a lépéseket, hogy klón a Git tárházban, és nyomja csak a fájlokat található a **szkriptek / ImageFactory** könyvtárban.

## <a name="create-a-build-and-connect-to-azure"></a>Build létrehozása és csatlakozás az Azure-hoz
Ezen a ponton a forrásfájlok at egy Git-tárházban tárolja az Azure DevOps-ban. Most be kell állítania egy folyamatot az Azure PowerShell futtatásához. Ezeket a lépéseket számos lehetőség közül választhat. Ebben a cikkben az egyszerűség kedvéért builddefiníciót használ, de együttműködik a DevOps Build, a DevOps-kiadás (egy vagy több környezet), más végrehajtási motorok, például a Windows Feladatütemező vagy bármely más, az Azure PowerShell végrehajtására alkalmas biztonsági résszel.

> [!NOTE]
> Egy fontos szempont, hogy vegye figyelembe, hogy néhány PowerShell-fájlok hosszú időt vesz igénybe, ha sok (10+) egyéni lemezképek létrehozása. Ingyenes üzemeltetett DevOps Build/Release ügynökök egy időtúlszállás 30 min, így nem tudja használni az ingyenes üzemeltetett ügynök, ha elkezd sok kép létrehozása. Ez az időtúltöltési kihívás vonatkozik bármilyen hám úgy dönt, hogy használja, jó, ha előre ellenőrizze, hogy meghosszabbíthatja a tipikus időtúllépést a hosszú ideig futó Azure PowerShell-parancsfájlok. Az Azure DevOps esetén használhatja a fizetős üzemeltetett ügynököket, vagy használhatja a saját buildelőügynökét.

1. A kezdéshez válassza a **Build beállítása** lehetőséget a DevOps-projekt kezdőlapján:

    ![Telepítés gomb](./media/set-up-devops-lab/setup-build-button.png)
2. Adja meg a build **nevét** (például: Lemezképek létrehozása és kézbesítése a DevTest Labs számára).
3. Jelöljön ki egy **üres** builddefiníciót, és válassza az **Alkalmaz** lehetőséget a build létrehozásához.
4. Ebben a szakaszban **kiválaszthatja a Build ügynök üzemeltetett.**
5. **Mentse** a builddefiníciót.

    ![Builddefiníció](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>A buildváltozók konfigurálása
A parancssori paraméterek egyszerűsítése érdekében foglalja magában azokat a kulcsértékeket, amelyek a lemezkép-gyárat buildváltozók halmazába irányítják. Válassza a **Változók** lapot, és megjelenik egy lista több alapértelmezett változóról. Az alábbiakban az Azure DevOps-ba beírandó változók listája található:


| Változó neve | Érték | Megjegyzések |
| ------------- | ----- | ----- |
| ConfigurationLocation (Konfigurációhelye) | /Scripts/ImageFactory/Configuration | Ez a **konfigurációs** mappa tárházának teljes elérési útja. Ha a fenti teljes tárta importálása kor a bal oldali érték helyes. Ellenkező esetben frissítse a konfiguráció helyére való pontot. |
| DevTestLabName | MyImageFactory (MyImageFactory) | Az Azure DevTest Labs laborjának neve, amelyet gyárként használnak a képek létrehozásához. Ha még nincs, hozzon létre egyet. Győződjön meg arról, hogy a labor ugyanabban az előfizetésben van, amelyhez a szolgáltatás végpontja rendelkezik hozzáféréssel. |
| ImageRetention (Képmegtartás) | 1 | Az egyes típusok menteni kívánt képeinek száma. Állítsa az alapértelmezett értéket 1-re. |
| MachinePassword gépjelszava | ******* | A virtuális gépek beépített rendszergazdai fiókjelszava. Ez egy átmeneti fiók, ezért győződjön meg róla, hogy biztonságos. A jobb oldali kis lakat ikon kiválasztásával biztosíthatja, hogy biztonságos karakterláncról legyen szó. |
| MachineUserName (Gépfelhasználó neve) | ImageFactoryUser | A virtuális gépek beépített felügyeleti fiókfelhasználóneve. Ez egy átmeneti számla. |
| StandardTimeoutPerc | 30 | Az időmeghosszabbítás, amelyet meg kell várnunk a rendszeres Azure-műveletekre. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | Az előfizetés azonosítója, ahol a tesztkörnyezet létezik, és amelyhez a szolgáltatás végpontja rendelkezik hozzáféréssel. |
| VMSize | Standard_A3 | A **létrehozási** lépéshez használandó virtuális gép mérete. A létrehozott virtuális gépek átmenetiek. A méretnek a [laborszámára engedélyezett](devtest-lab-set-lab-policy.md)nek kell lennie. Ellenőrizze, hogy van-e elegendő [előfizetési magkvóta.](../azure-resource-manager/management/azure-subscription-service-limits.md)

![Változók létrehozása](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
A következő lépés a szolgáltatásnév beállítása. Ez egy identitás az Azure Active Directoryban, amely lehetővé teszi a DevOps buildügynök az Azure-ban a felhasználó nevében. A beállításához kezdje az Azure PowerShell-buildlépés első hozzáadásával.

1. Válassza **a Feladat hozzáadása**lehetőséget.
2. Keresse meg az **Azure PowerShellt.**
3. Miután megtalálta, válassza **a Hozzáadás** lehetőséget, ha hozzá szeretné adni a feladatot a buildhez. Ha ezt teszi, a feladat a bal oldalon jelenik meg hozzáadottként.

![PowerShell-lépés beállítása](./media/set-up-devops-lab/set-up-powershell-step.png)

Az egyszerű szolgáltatás beállításának leggyorsabb módja, ha hagyja, hogy az Azure DevOps tegye meg nekünk.

1. Válassza ki az imént hozzáadott **feladatot.**
2. Az **Azure Connection Type (Azure Connection Type) mezőben**válassza az Azure Resource **Manager**lehetőséget.
3. Válassza a **Kezelés** hivatkozást az egyszerű szolgáltatás beállításához.

További információkat [ebben a blogbejegyzésben](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) talál. Ha **kiválasztja** a Kezelés hivatkozást, a devops megfelelő helyen fog landolni (a blogbejegyzés második képernyőképe) az Azure-hoz való kapcsolat beállításához. Győződjön meg arról, hogy az **Azure Resource Manager service endpoint** lehetőséget választja, amikor ezt állítja be.

## <a name="complete-the-build-task"></a>A buildelési feladat végrehajtása
Ha kiválasztja a build feladatot, a jobb oldali ablaktáblán minden részletet látni fog, amelyet ki kell töltenie.

1. Először nevezze el a build feladatot: **Virtuális gépek létrehozása.**
2. Válassza ki az Azure **Resource Manager** kiválasztásával létrehozott **egyszerű szolgáltatást**
3. Válassza ki a **szolgáltatás végpontot**.
4. A **Parancsfájl elérési útja csoportban**válassza a **... (Ellipszis)** a jobb oldalon.
5. Keresse meg a **MakeGoldenImageVMs.ps1** parancsfájlt.
6. A parancsfájlparamétereknek így kell kinézniük:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![A builddefiníció befejezése](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>A build várólistára állítása
Ellenőrizzük, hogy minden helyesen van-e beállítva egy új build sorba állásával. A build futása közben váltson az [Azure Portalra,](https://portal.azure.com) és válassza ki a **rendszerkép gyári** laborjában lévő összes virtuális gépet, és ellenőrizze, hogy minden megfelelően működik-e. Látnia kell, hogy három virtuális gép jön létre a laborban.

![Virtuális gépek a laborban](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>További lépések
Az Azure DevTest Labs alapján a lemezképgyár beállításának első lépése befejeződött. A sorozat következő cikkében általánossá teszi a virtuális gépeket, és egyéni lemezképekre menti őket. Aztán szétosztod őket az összes többi laborban. Lásd a sorozat következő cikkét: [Egyéni képek mentése és több laborba való terjesztés.](image-factory-save-distribute-custom-images.md)
