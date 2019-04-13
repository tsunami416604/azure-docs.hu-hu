---
title: Egy rendszerkép-előállító Futtatás az Azure DevOps, az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogy egy egyéni rendszerkép-előállító létrehozása az Azure DevTest Labs szolgáltatásban.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 5a3d6e51a71f6aab742fe042d6e6e281192319a4
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523018"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Rendszerkép-előállító futtatása az Azure DevOpsból
Ez a cikk ismerteti az Azure DevOps (korábbi nevén Visual Studio Team Services) a rendszerkép gyári futtatásához szükséges összes előkészített.

> [!NOTE]
> Bármely vezénylési motor működni fog! Az Azure DevOps használata nem kötelező. A kép gyári futtatja a használatával az Azure PowerShell-szkriptek, így lehet manuálisan, futtassa a Windows Feladatütemezőt, más CI/CD rendszerek használatával, és így tovább.

## <a name="create-a-lab-for-the-image-factory"></a>A kép Factory labor létrehozása
Az első lépés a lemezkép gyári beállítása az Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet létrehozása. A laborgyakorlat a kép gyári tesztkörnyezetben, ahol hozunk létre a virtuális gépeket, és mentse az egyéni lemezképek. A laborgyakorlat számít az átfogó kép előállító folyamat részeként. Ha a labor létrehozása, ügyeljen arra, hogy mentse a nevét, mivel azt később szüksége lesz rá.

## <a name="scripts-and-templates"></a>Parancsprogramok és sablonok
A következő lépéssel a lemezkép-előállítója csapata bevezetése az, hogy mi érhető el. A kép gyári parancsfájlok és sablonok érhető el nyilvánosan a [DevTest Labs GitHub-adattárat](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory). A következő áttekintése, amelyek az egyes elemek:

- Kép előállító. A gyökérmappa. 
    - Konfiguráció. A bemeneti kép gyári adatok
        - GoldenImages. Ez a mappa JSON-fájlok, amelyek egyéni rendszerképek definícióit tartalmazza.
        - Labs.json. A fájl ahol csapatok iratkozzon fel az adott egyéni rendszerképek kap.
- Parancsfájlok. A kép gyári összetevő.

Ebben a szakaszban található cikkek ezen parancsfájlok és sablonok további részleteket nyújtanak. 

## <a name="create-an-azure-devops-team-project"></a>Az Azure DevOps csapata projekt létrehozása
Az Azure DevOps segítségével tárolja a forráskódot, és futtassa az Azure PowerShell egy helyen. Ismétlődő futtatások és naprakész állapotban tarthatja a lemezképeket is ütemezhető. Nincsenek diagnosztizálhatja a problémákat a naplózás a helyes üzemeltetnek.  A Azure DevOps azonban nem követelmény, bármely hasznosítása/motor, amely képes csatlakozni az Azure, és futtathatja az Azure PowerShell használatával.

Ha rendelkezik egy meglévő DevOps-fiók vagy a projekthez használni kívánt, kihagyhatja ezt a lépést.

Első lépésként hozzon létre egy ingyenes fiókot az Azure DevOps. Látogasson el https://www.visualstudio.com/ válassza **kezdje meg ingyenesen** jobb alatt **Azure DevOps** (korábbi nevén vsts-ben). Szüksége lesz, válasszon egy egyedi fióknevet, és ellenőrizze, hogy úgy dönteni, hogy a kód Git használata kezelheti. Miután ez elkészült, mentse az URL-cím a csoportprojektet. Íme egy minta URL-cím: `https://<accountname>.visualstudio.com/MyFirstProject`.

## <a name="check-in-the-image-factory-to-git"></a>A Git-előállító lemezkép ellenőrzése
A PowerShell, sablonok és konfiguráció a lemezkép Factory találhatók a [nyilvános DevTest Labs GitHub-adattárat](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory). A leggyorsabb módja a kód az új csoportprojektet kerülnek a rendszer importálja a tárházban. Ez lekéri a teljes DevTest Labs-tárházban, (tehát kap további docs és példáink segítségével). 

1. Látogasson el az Azure DevOps-projekt, amely az előző lépésben létrehozott (URL-címe a következőhöz hasonló **https:\//\<accountname >.visualstudio.com/MyFirstProject**).
2. Válassza ki **importálása egy tárház**.
3. Adja meg a **clone URL-cím** a DevTest Labs adattárban: `https://github.com/Azure/azure-devtestlab`.
4. Válassza ki **importálás**.

    ![Importálja a Git-adattár](./media/set-up-devops-lab/import-git-repo.png)

Ha úgy dönt, hogy csak a pontosan mire van szükség (a gyári képfájlok) ellenőrizze, kövesse a lépéseket [Itt](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) a Git-tárház klónozása, és csak a található fájlok küldje le a **parancsfájlok/ImageFactory** könyvtár.

## <a name="create-a-build-and-connect-to-azure"></a>Build létrehozása és csatlakoztatása az Azure-bA
Ezen a ponton rendelkezik egy Git-adattárból az Azure DevOps tárolt forrásfájlok. Most kell folyamat beállítása az Azure PowerShell futtatásához. Nincsenek számtalan lehetőséget kínál, ezek a lépések végrehajtásához. Ebben a cikkben a builddefiníció az egyszerűség kedvéért használja, de DevOps Build, DevOps-kiadásban (egy vagy több környezetben), más végrehajtási motorok, például a Windows Feladatütemező vagy bármely más kihasználhatja, amelyekkel végrehajtható az Azure PowerShell-lel is működik.

> [!NOTE]
> Egy fontos pont figyelembe kell venni, hogy a PowerShell-fájlokat egy futtassa, amikor sokkal (10 +) egyéni rendszerképek létrehozása hosszú időt is igénybe. Ingyenes üzemeltetett DevOps Buildelési és kiadási ügynökei 30 perces időtúllépés rendelkezik, ezért nem használható az ingyenes üzemeltetett ügynök számos rendszerkép készítése elindítását követően. Érvényes időtúllépési mutatták függetlenül kihasználhatja használata mellett dönt, hogy, fontos meghozni győződjön meg arról, hogy a tipikus időtúllépéseket figyeli a hosszú ideig futó Azure PowerShell-parancsfájlok kiterjesztheti. Azure DevOps, esetén is vagy fizetős saját ügynökök vagy használ a saját fordító-ügynökhöz.

1. Válassza **állítsa be a Build** a kezdőlapon a DevOps-projekt:

    ![A telepítő Build gomb](./media/set-up-devops-lab/setup-build-button.png)
2. Adjon meg egy **neve** build (például: Hozhat létre, és lemezképeket kézbesíthet DevTest Labs). 
3. Válasszon egy **üres** builddefiníció, és válassza ki **alkalmaz** a build létrehozása. 
4. Ebben a szakaszban kiválaszthatja **üzemeltetett** a fordító-ügynökhöz. 
5. **Mentés** a builddefiníció.

    ![Builddefiníció](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>A build változók konfigurálása
Egyszerűsítése érdekében a parancssori paraméterek, a lemezkép gyári meghajtó build változók készletét kulcsértékeit beágyazására. Válassza ki a **változók** lapra, és megjelenik több alapértelmezett változók listáját. Itt látható a listában, adja meg az Azure DevOps változók:


| Változó neve | Érték | Megjegyzések |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Ez a teljes elérési útja a tárházban, a **konfigurációs** mappát. Ha importálta a fenti egész tárházban, a bal oldali értéke helyes-e. Ellenkező esetben frissítse, hogy a konfigurációs helyre mutasson. |
| DevTestLabName | MyImageFactory | A labor létrehozása az Azure DevTest Labs szolgáltatásban, mivel az előállító rendszerképek létrehozásához használt név. Ha még nincs fiókja, hozzon létre egyet. Győződjön meg arról, hogy a labor ugyanahhoz az előfizetéshez tartozik, amely hozzáfér a szolgáltatásvégpontot. |
| ImageRetention | 1 | Menti az egyes rendszerképek száma. Állítsa be az alapértelmezett érték 1-re. |
| MachinePassword | ******* | A virtuális gépek beépített rendszergazdai fiók jelszava. Ez egy átmeneti fiók, ezért győződjön meg arról, hogy biztonságosan. Válassza ki a kis lakat ikonra, hogy egy biztonságos karakterláncot a jobb oldalon. |
| MachineUserName | ImageFactoryUser | A beépített rendszergazdai fiók a virtuális gépek felhasználóneve. A rendszer egy átmeneti fiókot. |
| StandardTimeoutMinutes | 30 | Az időkorlát, hogy várnia kell rendszeres Azure műveleteket. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | Ha a labor létezik, és, amely a szolgáltatásvégpont hozzáféréssel rendelkezik az előfizetés azonosítója. |
| VMSize | Standard_A3 | A használni kívánt virtuális gép méretét a **létrehozás** . lépés. A létrehozott virtuális gépek átmeneti. A méretet kell lennie a hogy [engedélyezve van a tesztkörnyezethez](devtest-lab-set-lab-policy.md). Ellenőrizze, hogy nincs elegendő [előfizetés magkvótáját](../azure-subscription-service-limits.md). 

![Változók létrehozása](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
A következő lépés az egyszerű szolgáltatás beállítása. Ez az az Azure Active Directoryban, amely lehetővé teszi a fejlesztési és üzemeltetési fordító-ügynökhöz az Azure-ban a felhasználó nevében a művelethez használandó identitást. Állítsa be, hogy első lépésként adja hozzá, akkor első Azure PowerShell-Build lépés.

1. Válassza ki **feladat hozzáadása**.
2. Keresse meg **az Azure PowerShell**. 
3. Ha megtalálta, válassza ki a **Hozzáadás** a feladat hozzáadása a build. Ha így tesz, láthatja a feladat jelenik meg a bal oldalon, a hozzáadása során.

![Állítsa be a PowerShell lépés](./media/set-up-devops-lab/set-up-powershell-step.png)

Egyszerű szolgáltatás beállítása a leggyorsabb módot kínálni arra, hogy lehetővé teszik az Azure DevOps, tegye meg velünk a kapcsolatot. 

1. Válassza ki a **feladat** , az előzőekben adott hozzá.
2. A **Azure kapcsolattípus**, válassza a **Azure Resource Manager**. 
3. Válassza ki a **kezelés** állítsa be az egyszerű szolgáltatás mutató hivatkozást. 

További információkért lásd: Ez [blogbejegyzés](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Kiválasztásakor az **kezelés** a megfelelő helyen devops nyílik hivatkozás, (az ebben a blogbejegyzésben a második képernyőképet) a kapcsolatot az Azure-bA. Ügyeljen arra, hogy válasszon **Azure Resource Manager-szolgáltatásvégpont** ez beállítása során.

## <a name="complete-the-build-task"></a>Az összeállítási feladat befejezése
Ha az összeállítási feladat, látni fogja a részletekről a jobb oldali ablaktáblán, hogy ki kell tölteni. 

1. Először adja meg az összeállítási feladat: **Virtuális gépek létrehozása**. 
2. Válassza ki a **szolgáltatásnév** kiválasztásával létrehozott **Azure Resource Manager**
3. Válassza ki a **szolgáltatásvégpont**. 
4. A **parancsfájl elérési útján**válassza **... három (pont)**  a jobb oldalon.
5. Navigáljon a **MakeGoldenImageVMs.ps1** parancsfájlt. 
6. Szkriptparaméterek kell kinéznie: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Végezze el a build definíciója](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>A build várólista
Ellenőrizzük, hogy minden megfelelően állítsa be egy új fordítást message queuing által rendelkezik. Váltson a build futása közben a [az Azure portal](https://portal.azure.com) , és válassza ki a **összes virtuális gép** a lemezkép gyári tesztkörnyezetben, győződjön meg arról, hogy minden helyesen működik. Megjelenik a három virtuális gép létrehozása a lab-ben.

![A lab-ben a virtuális gépek](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>További lépések 
Az első lépés az Azure DevTest Labs alapján kép előállító beállítása befejeződött. A sorozat következő cikkben kap ezek általános és egyéni rendszerképek mentett virtuális gépek. Ezt követően rendelkezik a szükséges ossza el a többi teszttel. A sorozat következő cikkében talál: [Egyéni rendszerképek mentse, és több labs terjesztéséhez](image-factory-save-distribute-custom-images.md).
