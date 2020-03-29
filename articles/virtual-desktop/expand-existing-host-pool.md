---
title: Meglévő gazdagépkészlet bővítése új munkamenet-gazdagépekkel - Azure
description: Meglévő gazdagépkészlet kibontása új munkamenet-gazdagépekkel a Windows Virtuális asztal rendszerben.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: aee5195fe86fed3e631908a38d3bdb7d5e4883b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365219"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Meglévő gazdagépkészlet kibontása új munkamenet-gazdagépekkel

A gazdagépkészleten belüli használat felgyorsítása során előfordulhat, hogy az új terhelés kezeléséhez ki kell bővítenie a meglévő gazdagépkészletét az új munkamenet-gazdagépekkel.

Ez a cikk bemutatja, hogyan bonthatja ki a meglévő gazdakészletet az új munkamenet-gazdagépekkel.

## <a name="what-you-need-to-expand-the-host-pool"></a>Mire van szükség a gazdakészlet bővítéséhez?

Mielőtt elkezdené, győződjön meg arról, hogy létrehozott egy gazdakészletet és munkamenetgazda virtuális gépeket (VM-ek) az alábbi módszerek egyikével:

- [Az Azure Piactér ajánlata](./create-host-pools-azure-marketplace.md)
- [GitHub Azure Resource Manager-sablon](./create-host-pools-arm-template.md)
- [Gazdagépcsoport létrehozása a PowerShell-lel](./create-host-pools-powershell.md)

A következő információkra is szüksége lesz a gazdakészlet és a munkamenetgazda virtuális gépének első létrehozásakor:

- Virtuális gép mérete, lemezképe és névelőtagja
- Tartományi csatlakozás és a Windows virtuális asztal bérlői hitelesítő adatai
- Virtuális hálózat neve és alhálózatának neve

A következő három szakasz három módszer, amelyekkel kibonthatja a gazdakészletet. Bármelyik üzembe helyezési eszközzel megteheti, amivel elégedett.

>[!NOTE]
>A központi telepítési fázisban hibaüzenetek jelennek meg az előző munkamenet gazdagép virtuális gép erőforrásaihoz, ha azok jelenleg le vannak állítva. Ezek a hibák azért fordulnak elő, mert az Azure nem tudja futtatni a PowerShell DSC-bővítményt annak ellenőrzéséhez, hogy a munkamenetgazda virtuális gépei megfelelően vannak-e regisztrálva a meglévő gazdakészlethez. Nyugodtan figyelmen kívül hagyhatja ezeket a hibákat, vagy elkerülheti a hibákat a meglévő gazdagépkészlet összes munkamenet-gazdagépvirtuális gépének elindításával a központi telepítési folyamat megkezdése előtt.

## <a name="redeploy-from-azure"></a>Újratelepítés az Azure-ból

Ha már létrehozott egy gazdakészletet és munkamenetgazda virtuális gépeket az [Azure Marketplace-ajánlat](./create-host-pools-azure-marketplace.md) vagy a [GitHub Azure Resource Manager sablon](./create-host-pools-arm-template.md)használatával, újratelepítheti ugyanazt a sablont az Azure Portalról. A sablon újratelepítése automatikusan újraírja az eredeti sablonba megadott összes információt, kivéve a jelszavakat.

Az alábbiakban bemutatja, hogyan helyezheti üzembe újra az Azure Resource Manager-sablont a gazdakészlet bővítéséhez:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Az Azure Portal tetején található keresősávban keresse meg az **Erőforráscsoportok at,** és válassza ki az elemet a **Szolgáltatások**csoportban.
3. Keresse meg és jelölje ki azt az erőforráscsoportot, amelyet a gazdagépkészlet létrehozásakor hozott létre.
4. A böngésző bal oldalán lévő panelen válassza a **Központi telepítések**lehetőséget.
5. Válassza ki a gazdakészlet létrehozásának folyamatához megfelelő központi telepítést:
     - Ha az eredeti gazdakészletet az Azure Marketplace-ajánlattal hozta létre, válassza ki az **rds.wvd-provision-host-pool**kezdetű központi telepítést.
     - Ha az eredeti gazdakészletet a GitHub Azure Resource Manager sablonnal hozta létre, válassza ki a Microsoft.Template nevű központi **telepítést.**
6. Válassza **az Újratelepítés**lehetőséget.
     
     >[!NOTE]
     >Ha a sablon nem települ újra automatikusan, amikor az Újratelepítés lehetőséget **választja,** válassza a **Sablon** lehetőséget a böngésző bal oldalán lévő panelen, majd a **Telepítés**lehetőséget.

7. Válassza ki azt az erőforráscsoportot, amely a meglévő gazdakészletben lévő aktuális munkamenetgazda virtuális gépeket tartalmazza.
     
     >[!NOTE]
     >Ha olyan hibaüzenet et lát, amely arra utasítja, hogy válasszon másik erőforráscsoportot annak ellenére, hogy a megadott helyes, válasszon egy másik erőforráscsoportot, majd válassza ki az eredeti erőforráscsoportot.

8. Adja meg a következő URL-címet a *_artifactsLocation:*`https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Adja meg a kívánt munkamenet-állomások új számát az *Rdsh Példányok száma mezőbe.* Ha például a gazdakészletet öt munkamenet-házigazdáról nyolcra bővíti, írja be a **8**értéket.
10. Adja meg ugyanazt a meglévő tartományjelszót, amelyet a meglévő tartomány egyszerű kiszolgálóújához használt. Ne módosítsa a felhasználónevet, mert az hibát fog okozni a sablon futtatásakor.
11. Adja meg ugyanazt a bérlői rendszergazdai jelszót, amelyet a *bérlői rendszergazdai upn vagy alkalmazásazonosítóhoz*megadott felhasználó- vagy alkalmazásazonosítóhoz használt. Még egyszer, ne változtassa meg a felhasználónevet.
12. Töltse ki a beküldést a gazdagépkészlet bővítéséhez.

## <a name="run-the-azure-marketplace-offering"></a>Az Azure Marketplace-ajánlat futtatása

Kövesse a [Host Pool létrehozása az Azure Marketplace használatával című](./create-host-pools-azure-marketplace.md) útmutató utasításait, amíg el nem éri az Azure [Marketplace-ajánlat futtatását egy új gazdakészlet kiépítéséhez.](./create-host-pools-azure-marketplace.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool) Amikor eljut arra a pontra, meg kell adnia a következő információkat minden laphoz:

### <a name="basics"></a>Alapvető beállítások

Az ebben a szakaszban szereplő összes értéknek meg kell egyeznie azzal, amit a gazdakészlet és a munkamenetgazda virtuális gépeinek első létrehozásakor megadott, kivéve az *alapértelmezett asztali felhasználókat:*

1.    *Előfizetés esetén*válassza ki azt az előfizetést, amelyhez először létrehozta a gazdakészletet.
2.    *Az Erőforráscsoport*esetében válassza ki ugyanazt az erőforráscsoportot, ahol a meglévő gazdakészlet munkamenet-gazdagépei találhatók.
3.    *A Régió*területen válassza ki ugyanazt a régiót, ahol a meglévő gazdakészlet munkamenet-gazdavirtuális gépei találhatók.
4.    A *Hostpool-név mezőbe*írja be a meglévő állomáskészlet nevét.
5.    *Az Asztal típusnál*válassza ki a meglévő állomáskészletnek megfelelő asztaltípust.
6.    *Az alapértelmezett asztali felhasználók*számára adja meg azoknak a további felhasználóknak a vesszővel elválasztott listáját, akik be szeretné jelentkezni a Windows virtuális asztali ügyfelekbe, és az Azure Marketplace-ajánlat befejezése után hozzáférhet az asztalhoz. Ha például hozzá szeretne user3@contoso.com user4@contoso.com rendelni és user3@contoso.comuser4@contoso.comel szeretne érni, írja be a .
7.    Válassza a **Tovább lehetőséget : A virtuális gép konfigurálása**.

>[!NOTE]
>Az *alapértelmezett asztali felhasználók*kivételével minden mezőnek pontosan meg kell egyeznie a meglévő állomáskészletben beállított beállításokkal. Ha eltérés van, az új gazdagépkészletet eredményez.

### <a name="configure-virtual-machines"></a>Virtuális gépek konfigurálása

Ebben a szakaszban minden paraméterértéknek meg kell egyeznie azzal, amit a gazdakészlet és a munkamenetgazda virtuális gépeinek első létrehozásakor megadott, kivéve a virtuális gépek teljes számát. A megadott virtuális gépek száma a kibontott gazdakészletben lévő virtuális gépek száma lesz:

1. Válassza ki a virtuális gép méretét, amely megfelel a meglévő munkamenet-gazdavirtuális gépek.
    
    >[!NOTE]
    >Ha a keresett virtuális gép mérete nem jelenik meg a virtuális gép méretválasztójában, annak az az oka, hogy még nem vettük volna be az Azure Marketplace-eszközbe. Virtuális gép méretének igényléséhez hozzon létre egy kérést, vagy szavazzon egy meglévő kérelmet a [Windows Virtual Desktop UserVoice fórumon.](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)

2. A *Használati profil*, *az Összes felhasználó*és a Virtuális gépek *paramétereinek száma* testreszabásával megadhatja, hogy a gazdagépkészletben hány munkamenet-gazdagépet szeretne használni. Ha például a gazdakészletöt öt munkamenet-állomásról nyolcra bővíti, konfigurálja ezeket a beállításokat úgy, hogy 8 virtuális gépre jusson.
3. Adjon meg egy előtagot a virtuális gépek nevéhez. Ha például az "előtag" nevet adja meg, a virtuális gépek neve "előtag-0", "előtag-1" és így tovább lesz.
4. Válassza a **Tovább : Virtuálisgép beállításai**lehetőséget .

### <a name="virtual-machine-settings"></a>A virtuális gép beállításai

Ebben a szakaszban az összes paraméterértéknek meg kell egyeznie azzal, amit a gazdakészlet és a munkamenetgazda virtuális gépeinek első létrehozásakor megadott:

1. A *Lemezképforrás* és *a Kép-operációs rendszer verziójához*adja meg ugyanazokat az adatokat, amelyeket a gazdagépkészlet első létrehozásakor megadott.
2. *AD tartomány csatlakozzon* az egyszerű névhez és a kapcsolódó jelszavakhoz, adja meg ugyanazokat az adatokat, amelyeket a gazdakészlet első létrehozásakor megadott, hogy csatlakozzon a virtuális gépekhez az Active Directory tartományhoz. Ezeket a hitelesítő adatokat a virtuális gépeken egy helyi fiók létrehozásához használjuk. Ezek a helyi fiókok alaphelyzetbe állítása a hitelesítő adatok későbbi módosításához.
3. A virtuális hálózati információk, válassza ki ugyanazt a virtuális hálózatot és alhálózatot, ahol a meglévő gazdagépkészlet munkamenet gazdagép virtuális gépek találhatók.
4. Válassza a **Tovább lehetőséget: A Windows virtuális asztal adatainak konfigurálása**.

### <a name="windows-virtual-desktop-information"></a>A Windows virtuális asztal adatai

Ebben a szakaszban az összes paraméterértéknek meg kell egyeznie azzal, amit a gazdakészlet és a munkamenetgazda virtuális gépeinek első létrehozásakor megadott:

1. A *Windows virtuális asztal bérlői csoport nevéhez*adja meg a bérlői csoportot tartalmazó bérlői csoport nevét. Hagyja, hogy az alapértelmezett, kivéve, ha adott bérlői csoport nevét.
2. A *Windows virtuális asztal bérlői neve*esetén adja meg annak a bérlőnek a nevét, amelynek létrehozza ezt a gazdakészletet.
3. Adja meg ugyanazokat a hitelesítő adatokat, amelyeket a gazdakészlet és a munkamenetgazda virtuális gépének első létrehozásakor használt. Ha egyszerű szolgáltatásszolgáltatást használ, adja meg az Azure Active Directory-példány azonosítóját, ahol az egyszerű szolgáltatás található.
4. Válassza a **Tovább lehetőséget : Véleményezés + létrehozás**lehetőséget.

## <a name="run-the-github-azure-resource-manager-template"></a>A GitHub Azure Resource Manager-sablon futtatása

Kövesse az [Azure Resource Manager sablon futtatása az új gazdagépkészlet kiépítéséhez,](./create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) és adja meg az összes azonos paraméterértékeket, kivéve az *Rdsh példányok száma.* Adja meg a munkamenet gazdagépvirtuális gépeinek számát a gazdakészletben a sablon futtatása után. Ha például a gazdakészletet öt munkamenet-házigazdáról nyolcra bővíti, írja be a **8**értéket.

## <a name="next-steps"></a>További lépések

Most, hogy bővítette a meglévő gazdakészletét, bejelentkezhet egy Windows virtuális asztali ügyfélprogramba, hogy tesztelje őket egy felhasználói munkamenet részeként. A munkamenethez az alábbi ügyfelek bármelyikével csatlakozhat:

- [Kapcsolódás a Windows asztali ügyféllel](./connect-windows-7-and-10.md)
- [Kapcsolódás a webügyféllel](./connect-web.md)
- [Kapcsolódás az Android-ügyféllel](./connect-android.md)
- [Kapcsolódás a macOS-ügyfélhez](./connect-macos.md)
- [Kapcsolódás az iOS-ügyfélhez](./connect-ios.md)
