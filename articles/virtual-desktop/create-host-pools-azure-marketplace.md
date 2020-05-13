---
title: Windows rendszerű virtuális asztali címkészlet Azure Portal – Azure
description: Windows rendszerű virtuális asztali címkészlet létrehozása a Azure Portal használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d9effbe29917c774279b6e9d203f44d5ad5c72e2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121048"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Oktatóanyag: állomáslista létrehozása a Azure Portal

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). A Windows Vritual asztali 2019 környezettel létrehozott cikkek nem kezelhetők a Azure Portal.
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A gazdagép-készletek egy vagy több azonos virtuális gép (VM) gyűjteményei, amelyek a Windows rendszerű virtuális asztali környezetekben találhatók. Mindegyik gazdagép tartalmazhatja azt az alkalmazáscsoport-csoportot, amelyet a felhasználók a fizikai asztalon lévők használatával kezelhetnek.

Ebből a cikkből megtudhatja, hogyan hozhat létre egy Windows rendszerű virtuális asztali környezethez tartozó gazdagépet a Azure Portal használatával. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a Windows rendszerű virtuális asztali gazdagépek létrehozásához, egy Azure-előfizetésben található virtuális gépekkel rendelkező erőforráscsoport létrehozásához, a virtuális gépek Azure Active Directory (AD) tartományhoz való csatlakoztatásához, valamint a Windows rendszerű virtuális asztali gépek regisztrálásához.

## <a name="prerequisites"></a>Előfeltételek

A gazdagépek létrehozásához a következő paramétereket kell megadnia:

- A virtuális gép rendszerképének neve
- Virtuális gép konfigurációja
- Tartomány és hálózat tulajdonságai
- Windows rendszerű virtuális asztali gazdagép-készlet tulajdonságai

Emellett a következő dolgokat is ismernie kell:

- A használni kívánt rendszerkép forrása. Ez az Azure-katalógusból vagy egy egyéni rendszerkép?
- A tartományhoz való csatlakozáshoz szükséges hitelesítő adatok.

Győződjön meg arról is, hogy regisztrálta a Microsoft. DesktopVirtualization erőforrás-szolgáltatót. Ha még nem tette meg, lépjen az **előfizetések** elemre, válassza ki a Yoru-előfizetés nevét, majd válassza az **Azure Resource Providers**elemet.

Amikor létrehoz egy Windows rendszerű virtuális asztali címkészletet a Azure Resource Manager sablonnal, létrehozhat egy virtuális gépet az Azure-katalógusból, egy felügyelt képből vagy egy nem felügyelt rendszerképből. A virtuálisgép-lemezképek létrehozásával kapcsolatos további információkért lásd: [Windows VHD vagy VHDX előkészítése az Azure-ba való feltöltéshez](../virtual-machines/windows/prepare-for-upload-vhd-image.md) , valamint [egy általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban](../virtual-machines/windows/capture-image-resource.md).

Ha még nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , mielőtt elkezdené ezeket az utasításokat.

## <a name="begin-the-host-pool-setup-process"></a>A gazdagép-készlet telepítési folyamatának megkezdése

Az új címkészlet létrehozásának megkezdéséhez:

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.

2. Adja meg a **Windows rendszerű virtuális asztalt** a keresőmezőbe, majd keresse meg és válassza ki a **Windows rendszerű virtuális asztali** szolgáltatások elemet.

3. A **Windows rendszerű virtuális asztal** áttekintése lapon válassza **a gazdagép létrehozása**lehetőséget.

4. Az **alapvető beállítások** lapon válassza ki a megfelelő előfizetést a Project Details (projekt részletei) területen.

5. Vagy válassza az új létrehozása lehetőséget az új erőforráscsoport **létrehozásához** , vagy válasszon ki egy meglévő erőforráscsoportot a legördülő menüből.

6. Adjon egyedi nevet a gazdagép-készletnek.

7. A hely mezőben válassza ki azt a régiót, ahol létre szeretné hozni az alkalmazáskészletet a legördülő menüből.
   
   A kiválasztott régiókkal társított Azure földrajz a gazdagép-készlet és a hozzá kapcsolódó objektumok metaadatait fogja tárolni. Győződjön meg arról, hogy a földrajzi helyen belüli régiókat választja, amelyekben a szolgáltatás metaadatait tárolni szeretné.

     ![A Azure Portal az USA keleti régiójában kiválasztott Location (hely) mezőt bemutató képernyőkép. A mező mellett a "metaadatok az USA keleti régiójában lesznek tárolva."](media/portal-location-field.png)

8. Az alkalmazáskészlet típusa területen válassza ki, hogy a gazdagép **személyes** vagy **készletezési**készlettel rendelkezik-e.

    - Ha a **személyes**lehetőséget választja, válassza az **automatikus** vagy a **közvetlen** lehetőséget a hozzárendelés típusa mezőben.

      ![A hozzárendelés típusa mező legördülő menüjének képernyőképe. A felhasználó automatikus beállítást adott meg.](media/assignment-type-field.png)

9. Ha a **készletezett**lehetőséget választja, adja meg a következő adatokat:

     - A **munkamenetek**maximális száma lehetőségnél adja meg, hogy legfeljebb hány felhasználót szeretne terheléselosztással elválasztani egyetlen munkamenet-gazdagépre.
     - **Terheléselosztási algoritmus**esetén a használati minta alapján válassza a szélesség – első vagy a mélység – első lehetőséget.

       ![A hozzárendelés típus mezőjének képernyőképe a "készletezett" beállítással. A felhasználó fölé viszi a kurzort a szélesség – először a terheléselosztás legördülő menüben.](media/pooled-assignment-type.png)

10. Válassza a Next (tovább) lehetőséget **: virtuális gép adatai**.

11. Ha már létrehozott egy virtuális gépet, és használni szeretné őket az új gazdagép-készlettel, válassza a **nem**lehetőséget. Ha új virtuális gépeket szeretne létrehozni, és regisztrálja őket az új gazdagép-készletben, válassza az **Igen**lehetőséget.

Most, hogy elvégezte az első részt, térjünk át a telepítési folyamat következő részére, ahol létrehozjuk a virtuális gépet.

## <a name="virtual-machine-details"></a>Virtuális gép adatai

Most, hogy az első részen vagyunk, be kell állítania a virtuális gépet.

A virtuális gép beállítása a gazdagép-készlet telepítési folyamatán belül:

1. Az erőforráscsoport területen válassza ki azt az erőforráscsoportot, amelyben létre szeretné hozni a virtuális gépeket. Ez lehet egy másik erőforráscsoport, mint a gazdagéphez használt készlet.

2. Válassza ki azt a **virtuálisgép-régiót** , amelyben létre szeretné hozni a virtuális gépeket. Megegyeznek a gazdagéphez kiválasztott régióval, vagy eltérőek lehetnek.

3. Ezután válassza ki a létrehozni kívánt virtuális gép méretét. Megtarthatja az alapértelmezett méretet is, vagy a méret **módosításához válassza a méret módosítása** lehetőséget. Ha a **méret módosítása**lehetőséget választja, a megjelenő ablakban válassza ki a munkaterheléshez megfelelő virtuális gép méretét.

4. A virtuális gépek száma területen adja meg a gazdagéphez létrehozni kívánt virtuális gépek számát.

    >[!NOTE]
    >A telepítési folyamat akár 400 virtuális GÉPET is létrehozhat a gazdagép beállítása közben, és minden egyes virtuálisgép-beállítási folyamat négy objektumot hoz létre az erőforráscsoporthoz. Mivel a létrehozási folyamat nem ellenőrzi az előfizetési kvótát, győződjön meg arról, hogy a megadott virtuális gépek száma az Azure-beli virtuális gépen, valamint az erőforráscsoport és az előfizetés API-korlátain belül van. A gazdagép-készlet létrehozása után további virtuális gépeket adhat hozzá.

5. Ezt követően adjon meg egy **előtagot** a virtuális gépek számára a telepítési folyamat által létrehozott névvel. Az utótag a 0 értéktől `-` kezdődő számmal lesz ellátva.

6. Ezután válassza ki a virtuális gép létrehozásához használni kívánt rendszerképet. Kiválaszthatja a **Katalógus vagy a** **tárolási blob**lehetőséget.

    - Ha a katalógus lehetőséget **választja, válassza ki a**javasolt rendszerképek egyikét a legördülő menüből:

      - Windows 10 Enterprise multi-session, Version 1909 + Office 365 ProPlus – Gen 1
      - Windows 10 Enterprise multi-session, Version 1909 – Gen 1
      - Windows Server 2019 Datacenter – Gen1

     Ha nem látja a kívánt rendszerképet, válassza az **összes rendszerkép és lemez tallózása**lehetőséget, amely lehetővé teszi, hogy a katalógusban vagy a Microsoft által biztosított lemezképet vagy más közzétevőket is válasszon egy másik rendszerképben.

     ![A piactér képernyőképe a Microsoft által megjelenített rendszerképek listájáról.](media/marketplace-images.png)

     Megnyithatja **az elemeket** , és kiválaszthat egy már feltöltött egyéni rendszerképet is.

     ![Képernyőkép a saját elemek lapról.](media/my-items.png)

    - Ha a **Storage blob**lehetőséget választja, a saját rendszerképét a Hyper-V használatával vagy egy Azure-beli virtuális gépen is kihasználhatja. Mindössze annyit kell tennie, hogy megadja a rendszerkép helyét a Storage-blobban URI-ként.

7. Válassza ki, hogy a virtuális gépek milyen operációsrendszer-lemezeket szeretnének használni: standard SSD, prémium SSD vagy standard HDD.

8. A hálózat és biztonság területen válassza ki azt a virtuális hálózatot és alhálózatot, ahol a létrehozni kívánt virtuális gépeket el szeretné helyezni. Győződjön meg arról, hogy a virtuális hálózat tud csatlakozni a tartományvezérlőhöz, mert a virtuális hálózaton belüli virtuális gépeket a tartományhoz kell csatlakoztatnia. Ezután válassza ki, hogy szeretne-e nyilvános IP-címet használni a virtuális gépekhez. Azt javasoljuk, hogy válassza a **nem**lehetőséget, mert a magánhálózati IP-cím biztonságosabb.

9. Válassza ki a kívánt biztonsági csoport típusát: **Alapszintű**, **speciális**vagy **nincs**.

    Ha az **alapszintű**lehetőséget választja, ki kell választania, hogy meg kívánja-e nyitni a bejövő portokat. Ha az **Igen**lehetőséget választja, akkor a bejövő kapcsolatok engedélyezéséhez válasszon a szabványos portok listájából.

    >[!NOTE]
    >A nagyobb biztonság érdekében javasoljuk, hogy ne nyissa meg a nyilvános bejövő portokat.

    ![A biztonsági csoport oldalának képernyőképe, amely megjeleníti az elérhető portok listáját a legördülő menüben.](media/available-ports.png)
    
    Ha a **speciális**lehetőséget választja, válasszon ki egy már konfigurált, meglévő hálózati biztonsági csoportot.

10. Ezután válassza ki, hogy szeretné-e a virtuális gépeket egy adott tartományhoz és szervezeti egységhez csatlakoztatni. Ha az **Igen**lehetőséget választja, adja meg azt a tartományt, amelyhez csatlakozni szeretne. Hozzáadhat egy adott szervezeti egységet is, amelyben a virtuális gépeket be szeretné állítani.

11. A rendszergazdai fiók területen adja meg a kiválasztott virtuális hálózat Active Directory-tartomány rendszergazdájához tartozó hitelesítő adatokat.

12. Válassza ki a **munkaterületet**.

Ezzel készen áll arra, hogy elindítsa a gazdagép készletének következő fázisát: az alkalmazás csoportjának regisztrálása munkaterületre.

## <a name="workspace-information"></a>Munkaterület-információk

A gazdagép-készlet telepítési folyamata alapértelmezés szerint létrehoz egy asztali alkalmazáscsoport-csoportot. Ahhoz, hogy a gazdagép a kívánt módon működjön, közzé kell tennie ezt az erőforráscsoportot felhasználók vagy felhasználói csoportok számára, és regisztrálnia kell az alkalmazást egy munkaterületen. 

Az asztali alkalmazás csoportjának regisztrálása munkaterületre:

1. Válassza az **Igen** lehetőséget.

   Ha a **nem**lehetőséget választja, akkor később regisztrálhatja az alkalmazást, de azt javasoljuk, hogy a munkaterület regisztrációját a lehető leghamarabb végezze el, amint a gazdagép-készlet megfelelően működik.

2. Ezután adja meg, hogy szeretne-e új munkaterületet létrehozni, vagy válasszon a meglévő munkaterületekről. Csak abban a helyen létrehozott munkaterületek lesznek regisztrálva az alkalmazásban, ahol a gazdagép-készletet is létrehozták.

3. Lehetőség van a **címkék**kiválasztására is.

    Itt hozzáadhat címkéket, így a metaadatokkal csoportosíthatja az objektumokat, hogy könnyebb legyen a rendszergazdák számára.

4. Ha elkészült, válassza a **felülvizsgálat + létrehozás**elemet. 

     >[!NOTE]
     >A felülvizsgálat + ellenőrzési folyamat nem ellenőrzi, hogy a Jelszó megfelel-e a biztonsági szabványoknak, vagy ha az architektúra helyes, ezért az ilyen dolgok bármelyikével kapcsolatos problémákat meg kell vizsgálnia. 

5. Tekintse át az üzemelő példány adatait, és győződjön meg róla, hogy minden helyesnek tűnik. Amikor elkészült, válassza a **Létrehozás** lehetőséget. Ez elindítja a telepítési folyamatot, amely a következő objektumokat hozza létre:

     - Az új gazdagép-készletet.
     - Egy asztali alkalmazás csoportja.
     - Munkaterület, ha úgy dönt, hogy létrehoz egy munkaterületet.
     - Ha úgy dönt, hogy regisztrálja az asztali alkalmazás csoportját, akkor a regisztráció befejeződött
     - Virtuális gépek, ha úgy dönt, hogy létrehozza őket, amelyek a tartományhoz csatlakoznak, és regisztrálva vannak az új gazdagép-készletben.
     - Egy Azure Resource Management-sablonhoz tartozó letöltési hivatkozás a konfiguráció alapján.

Ezt követően már készen is van!

## <a name="next-steps"></a>További lépések

Most, hogy elvégezte a gazdagép készletét, feltöltheti azt a RemoteApp-programok használatával. Ha többet szeretne megtudni a Windows rendszerű virtuális asztali alkalmazások kezeléséről, tekintse meg a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Alkalmazás-csoportok kezelése – oktatóanyag](./manage-app-groups.md)
