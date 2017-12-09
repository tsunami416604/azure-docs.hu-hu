---
title: "Hozzon létre virtuális Gépre kiterjedő környezetek és PaaS-erőforrások Azure Resource Manager-sablonok |} Microsoft Docs"
description: "Útmutató: virtuális Gépre kiterjedő környezetek és PaaS erőforrások létrehozása a Azure DevTest Labs szolgáltatásban az Azure Resource Manager-sablon"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: tarcher
ms.openlocfilehash: 381b3e5cc82e71dde4fe3a4266678dbd5919290d
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Hozzon létre virtuális Gépre kiterjedő környezetek és PaaS-erőforrások Azure Resource Manager-sablonok

A [Azure-portálon](http://go.microsoft.com/fwlink/p/?LinkID=525040) lehetővé teszi, hogy könnyen [létrehozása és a virtuális gép hozzáadása egy laborhoz](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm). Egyszerre csak egy virtuális gép létrehozása esetén ez működik. Azonban ha a környezetben több virtuális gépeket tartalmaz, minden virtuális gép rendelkezik külön-külön kell létrehozni. Például egy többrétegű webalkalmazást vagy a SharePoint-farm használata esetén egy olyan mechanizmus szükséges ahhoz, hogy egyetlen lépésben több virtuális gép létrehozásához. Azure Resource Manager-sablonok használatával mostantól határozhatnak meg, az infrastruktúra és az Azure-megoldás konfigurációját, és több virtuális gépek konzisztens állapotban ismételten telepítheti. Ez a funkció a következő előnyöket nyújtja:

- Az Azure Resource Manager-sablonok töltődnek be közvetlenül a (GitHub vagy Team Services Git) a verziókövetési tárházzal.
- Beállítása után a felhasználók létrehozásához egy olyan környezetben, miért más Azure-portálról egy Azure Resource Manager sablon kiadási [VM körrel](./devtest-lab-comparing-vm-base-image-types.md).
- Azure PaaS-erőforrások kiépítése az Azure Resource Manager sablon mellett IaaS virtuális gépeket a környezetben.
- A labor mellett az egyes virtuális gépek hozta létre alapjait más típusú környezetek költségét követhető nyomon.
- PaaS erőforrások jönnek létre, és megjelennek a költségkövetést; virtuális gép automatikus leállítási azonban nem vonatkozik a PaaS erőforrásokhoz.
- Felhasználók rendelkeznek az azonos virtuális gép szabályzatvezérlést környezetek single-labor virtuális gépeken vannak.

További információ a több [előnyei a Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager) központi telepítéséhez, frissítéséhez vagy törléséhez a labor erőforrásokat egy művelettel.

> [!NOTE]
> Használatakor a Resource Manager-sablon alapján további tesztlabor virtuális gépek létrehozásához, vannak eltérések, szem előtt tartani, hogy több virtuális gépek vagy egyetlen virtuális gépek létrehozásakor. A virtuális gép használata Azure Resource Manager sablon ezek a különbségek részletesebben ismerteti.
>
>

## <a name="configure-azure-resource-manager-template-repositories"></a>Azure Resource Manager sablon adattárak konfigurálása

Az ajánlott eljárásokat és infrastruktúra-,-kódot, valamint configuration-,-kód egyikeként verziókezelő környezet sablonok kell kezelni. Azure DevTest Labs Ez az eljárás a következő, és betölti az összes Azure Resource Manager sablon közvetlenül a Githubból vagy VSTS Git tárházak a. Ennek eredményeképpen a Resource Manager-sablonok között a teljes kiadás ciklus során a tesztkörnyezetből az éles környezetbe használható.

Többféle végezhető el a tárházat az Azure Resource Manager-sablonok rendszerezésére szabályok:

- A fő sablonfájl névvel kell ellátni `azuredeploy.json`. 

    ![Kulcs Azure Resource Manager sablon fájlok](./media/devtest-lab-create-environment-from-arm/master-template.png)

- Egy paraméter fájlban definiált paraméterértékek használni kívánt, ha a paraméterfájl névvel kell ellátni `azuredeploy.parameters.json`.
- A paraméterek használhatók `_artifactsLocation` és `_artifactsLocationSasToken` összeállítani az parametersLink URI azonosítóját, így a DevTest Labs automatikusan a beágyazott sablonok kezeléséhez. Lásd: [hogyan Azure DevTest Labs egyszerűbbé teszi a beágyazott erőforrás-kezelő sablon központi telepítések tesztelési környezetben](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/) további információt.
- Metaadatok adhatja meg a sablon megjelenítési nevét és leírását lehet megadni. A metaadatok nevű fájlba kell `metadata.json`. A következő példa metaadatait tartalmazó fájl mutatja be a megjelenítendő név és leírás megadása: 

```json
{
 
"itemDisplayName": "<your template name>",
 
"description": "<description of the template>"
 
}
```

A következő lépések végigvezetik a tárház hozzáadása a tesztkörnyezet az Azure portál használatával. 

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **több szolgáltatások**, majd válassza ki **DevTest Labs** a listából.
1. Válassza ki a kívánt labor labs listájának megtekintéséhez.   
1. A labor paneljén válassza **konfigurációs és házirendek**.

    ![Konfigurációs és házirendek](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Az a **konfigurációs és házirendek** beállítások listáról válassza ki **Tárházak**. A **Tárházak** panel a tárolóhelyekkel a laborkörnyezetben hozzáadott sorolja fel. A tárház nevű `Public Repo` összes labs automatikusan létrejön, és csatlakozik a [DevTest Labs GitHub-tárház](https://github.com/Azure/azure-devtestlab) , amely tartalmazza a virtuális gép összetevők a használatra.

    ![Nyilvános tárház](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Válassza ki **Add +** hozzáadása az Azure Resource Manager sablon tárházba.
1. Ha a második **Tárházak** panel nyílik, adja meg a szükséges adatokat az alábbiak szerint:
    - **Név** -adja meg a tárház nevét, amely szolgál a tesztkörnyezetben.
    - **Git-klón URL-cím** -adja meg a GIT HTTPS-klón URL-cím a Githubból vagy a Visual Studio Team Services.  
    - **Fiókirodai** -fióknevének az Azure Resource Manager sablon definíciók eléréséhez. 
    - **Személyes hozzáférési jogkivonat** – a személyes hozzáférési jogkivonat segítségével érheti el biztonságosan a tárházhoz. Válassza ki ahhoz, hogy a jogkivonat a Visual Studio Team Services,  **&lt;sajatNev >> saját profil > biztonsági > nyilvános hozzáférési jogkivonat**. A token beszerzése a Githubról, jelölje be a profilképet kiválasztása után **beállítások > nyilvános hozzáférési jogkivonat**. 
    - **Mappák elérési útjaiban** -egyik két beviteli mezőt, adja meg a mappa elérési útját, - / - perjellel kezdődik, és a Git clone URI vagy viszonyítva az összetevő-definíciók (első beviteli mezőt) vagy az Azure Resource Manager sablon definíciók .   
    
        ![Nyilvános tárház](./media/devtest-lab-create-environment-from-arm/repo-values.png)

1. Amennyiben az összes kötelező mezőt van-e megadva, és adja át az ellenőrzést, válassza ki a **mentése**.

A következő szakasz végigvezeti környezetek létrehozása az Azure Resource Manager sablon alapján.

## <a name="create-an-environment-from-an-azure-resource-manager-template"></a>Környezet létrehozása az Azure Resource Manager-sablonok

Az Azure Resource Manager sablon tárház van konfigurálva a tesztkörnyezetben, ha a labor felhasználók olyan környezetet, az alábbi lépéseket az Azure-portál használatával hozhat létre:

1. Jelentkezzen be az [Azure Portalra](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza ki **több szolgáltatások**, majd válassza ki **DevTest Labs** a listából.
1. Válassza ki a kívánt labor labs listájának megtekintéséhez.   
1. A labor paneljén válassza **Add +**.
1. A **base válasszon** csempe megjeleníti az alap lemezképeket, a lista tetején található Azure Resource Manager-sablonok használatával. Válassza ki a kívánt Azure Resource Manager-sablon.

    ![Válasszon egy alapja](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. A a **Hozzáadás** panelen adja meg a **környezetnevet** érték. A környezet neve, a felhasználók számára, a laborban jelenik meg. Az Azure Resource Manager-sablon a fennmaradó beviteli mezők vannak definiálva. Ha az alapértelmezett értékek meg vannak határozva a sablonban vagy a `azuredeploy.parameter.json` fájl jelen, az alapértelmezett értékek megjelenítése a beviteli mezők. Típusú paraméterek *biztonságos karakterlánc*, használhatja a titkos kulcsokat, a laborban tárolt [titkos tárolójának](https://azure.microsoft.com/en-us/updates/azure-devtest-labs-keep-your-secrets-safe-and-easy-to-use-with-the-new-personal-secret-store).

    ![Hozzáadása panel](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > Nincsenek több paraméter - még akkor is, ha a megadott – a megjelenített értékek üres értékeket. Ezért, ha a felhasználók ezeket az értékeket rendel a paraméterek az Azure Resource Manager-sablonok, DevTest Labs nem az értékek megjelenítése; Ehelyett jeleníti meg az üres beviteli mezők Ha a labor felhasználók kell adjon meg egy értéket, a környezet létrehozásakor.
    > 
    > - GENERÁCIÓS EGYEDI
    > - -EGYEDI - GENERÁCIÓS [N]
    > - GENERÁCIÓS SSH-PUB-KULCS
    > - GENERÁCIÓS-JELSZÓ 
 
1. Válassza ki **Hozzáadás** a környezetet hozhat létre. A környezet elindítja az állapot megjelenő létrehozását azonnal a **a virtuális gépek** listája. Új erőforráscsoport automatikusan hozta létre a labor az Azure Resource Manager sablon definiált összes erőforrások biztosításához.
1. A környezet létrehozása után válassza ki a környezet a **a virtuális gépek** lista az erőforráscsoport panel megnyitásához, és keresse meg az összes környezetben kiosztott erőforrásokat.
    
    ![A virtuális gépek listája](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   A környezet csak által telepített virtuális gépek listájának megtekintése a környezetben bővítheti is.
    
    ![A virtuális gépek listája](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Kattintson bármelyik megtekintéséhez a rendelkezésre álló műveletek – például az adatlemezek, változó automatikus leállítási ideje, és több, az összetevők alkalmazása a környezetben.

    ![Környezet műveletek](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="next-steps"></a>Következő lépések
* A virtuális gép létrehozása után keresztül csatlakozhat a virtuális gép kiválasztásával **Connect** a virtuális gép paneljén.
* Megtekintése és kezelése az erőforrásoknak a környezetben a környezet kiválasztásával a **a virtuális gépek** lista a tesztkörnyezetben. 
* Megismerkedhet a [Azure Resource Manager sablonok Azure gyors üzembe helyezési sablon gyűjteményből](https://github.com/Azure/azure-quickstart-templates)
