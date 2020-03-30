---
title: Azure-fájlok megosztásának létrehozása és használata Windows virtuális gépeken
description: Ebben a rövid útmutatóban beállít egy Azure-fájlok megosztását az Azure Portalon, és csatlakoztatja egy Windows virtuális géphez. Csatlakozik a Fájlok megosztásához, és feltölt egy fájlt a Fájlok megosztásba. Ezután pillanatképet készíthet a Fájlok megosztásról, módosíthatja a fájlt a Fájlok megosztásban, és visszaállíthatja a Fájlok megosztás korábbi pillanatképét.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bbab0ee2eefe6e86c150d5bddab4f8e91a7c92d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75463914"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Rövid útmutató: Azure Files-megosztás létrehozása és kezelése Windows virtuális gépekkel

A cikk bemutatja az Azure Files-megosztás létrehozásának és használatának alapvető lépéseit. Ebben a rövid útmutatóban a hangsúly az Azure Files megosztás gyors beállításán van, így megtapasztalhatja a szolgáltatás működését. Ha részletesebb utasításokra van szüksége az Azure-fájlmegosztások létrehozásához és használatához a saját környezetében, olvassa el [az Azure-fájlmegosztás használata a Windows rendszerrel](storage-how-to-use-files-windows.md)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="prepare-your-environment"></a>A környezet előkészítése

Ebben a rövid útmutatóban a következő elemeket állíthatja be:

- Egy Azure-tárfiók és egy Azure-fájlmegosztás
- Windows Server 2016 Datacenter virtuális gép

### <a name="create-a-storage-account"></a>Create a storage account

Az Azure-fájlmegosztással való munka előtt létre kell hoznia egy Azure-tárfiókot. Egy általános célú v2 tárfiók hozzáférést biztosít az összes Azure Storage-szolgáltatások: blobok, fájlok, várólisták és táblák. A rövid útmutató létrehoz egy általános célú v2 tárfiókot, de a tárolási fiók létrehozásának lépései hasonlóak. A tárfiókok korlátlan számú megosztást tartalmazhatnak, a megosztásokban pedig tetszőleges számú fájlt tárolhat, egészen a tárfiókja kapacitásának korlátjáig.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Utána a fájlmegosztás létrehozása következik.

1. Amikor az Azure storage-fiók telepítése befejeződött, válassza **az Ugrás az erőforrásra**lehetőséget.
1. Válassza a **Fájlok lehetőséget** a tárfiók ablaktábláján.

    ![Fájlok kijelölése](./media/storage-files-quick-create-use-windows/click-files.png)

1. Válassza **a Fájlmegosztás lehetőséget.**

    ![A Fájlmegosztás hozzáadása gomb kijelölése](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nevezze el az új *fájlmegosztást qsfileshare* > írja be az "1" értéket a **Kvóta** > válassza a **Létrehozás lehetőséget.** A kvóta legfeljebb 5 TiB lehet, de ehhez a rövid útmutatóhoz csak 1 GiB szükséges.
1. Hozzon létre egy új txt fájlt *qsTestFile* néven a helyi számítógépen.
1. Jelölje ki az új fájlmegosztást, majd a fájlmegosztás helyén válassza a **Feltöltés**lehetőséget.

    ![Fájl feltöltése](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Tallózással keresse meg azt a helyet, ahol létrehozta a .txt fájlt, > válassza a *qsTestFile.txt* fájlt, > válassza a **Feltöltés lehetőséget.**

Eddig létrehozott egy Azure storage-fiókot és egy fájlmegosztást egy fájllal az Azure-ban. Ezután hozza létre az Azure virtuális gép a Windows Server 2016 Datacenter, hogy képviselje a helyszíni kiszolgáló ebben a rövid útmutatóban.

### <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

1. Ezután bontsa ki a menüt a portál bal oldalán, és válassza ki az **Erőforrás létrehozása** lehetőséget az Azure portál bal felső sarkában.
1. Az **Azure Marketplace-erőforrások** listájának keresőmezőjében keresse meg és válassza a **Windows Server 2016 Datacenter**elemet, majd válassza a **Létrehozás lehetőséget.**
1. Az **Alapok lap** **Project részletei**csoportjában válassza ki a rövid útmutatóhoz létrehozott erőforráscsoportot.

   ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. A **Példány adatai csoportban**adja meg a vm *qsVM*nevet.
1. Hagyja bejelölve az alapértelmezett beállításokat a **Régió**, **Rendelkezésre állási beállítások**, **Kép** és **Méret** mezőkben.
1. A **Rendszergazdafiók csoportban**adja hozzá a *VMadmin* **felhasználónevet,** és adja meg a virtuális gép **jelszavát.**
1. A **Bejövőport-szabályok** területen válassza a **Kijelölt portok engedélyezése**lehetőséget, majd válassza az **RDP (3389)** és a **HTTP** elemeket a legördülő listából.
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Kattintson a **Létrehozás** gombra. Az új virtuális gép létrehozása eltarthat pár percig.

1. A virtuális gép üzembe helyezése befejezése után válassza **az Ugrás az erőforrásra**lehetőséget.

Ezen a ponton már létrehozta az új virtuális gépet és csatolt egy adatlemezt. Most pedig csatlakoznunk kell a virtuális géphez.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

1. Válassza a **Csatlakozás** lehetőséget a virtuális gép tulajdonságai lapon.

   ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. A Csatlakozás a **virtuális géphez** lapon tartsa meg az alapértelmezett beállításokat, hogy **IP-cím** szerint csatlakozzon a *3389-es* **porton** keresztül, és válassza **az RDP-fájl letöltése**lehetőséget.
1. Nyissa meg a letöltött RDP-fájlt, és amikor a rendszer kéri, válassza a **Csatlakozás** lehetőséget.
1. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet *localhost\username*néven, ahol &lt;a felhasználónév&gt; a virtuális géphez létrehozott virtuális gép rendszergazdai felhasználóneve. Írja be a virtuális géphez létrehozott jelszót, majd kattintson az **OK gombra.**

   ![További lehetőségek](./media/storage-files-quick-create-use-windows/local-host2.png)

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. válassza az **Igen** vagy a **Folytatás** lehetőséget a kapcsolat létrehozásához.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Az Azure-fájlmegosztás hozzárendelése Windows-meghajtóhoz

1. Az Azure Portalon keresse meg a *qsfileshare* fájlmegosztást, és válassza a **Csatlakozás lehetőséget.**
1. Másolja a második doboz tartalmát a **Jegyzettömbbe.**

   ![Az UNC-útvonal az Azure Files Csatlakozás oldaláról](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. A virtuális gépben nyissa meg a **Fájlkezelőt,** és válassza az Ez a **számítógép** lehetőséget az ablakban. Ez a beállítás megváltoztatja a menüszalagon elérhető menüket. A **Számítógép** menüben válassza a **Hálózati meghajtó leképezése parancsot.**
1. Válassza ki a meghajtó betűjelét, és írja be az UNC-útvonalat. Ha követte az elnevezési javaslatokat ebben a rövid útmutatóban, másolja a * \\qsstorageacct.core.windows.net\qsfileshare fájlt a* **Jegyzettömbből.**

   Ellenőrizze, hogy mindkét jelölőnégyzet be van-e jelölve.

   ![A Hálózati meghajtó csatlakoztatása párbeszédpanel képernyőképe](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Válassza a **Finish** (Befejezés) elemet.
1. A **Windows biztonság** párbeszédpanelén:

   - A Jegyzettömbből másolja az AZURE\ készletfiók nevét, és illessze be a **Windows biztonság** párbeszédpanelére felhasználónévként. Ha a rövid útmutatóban követte az elnevezési javaslatokat, másolja az *AZURE\qsstorageacct a*.
   - A Jegyzettömbből másolja a tárfiók kulcsát, és illessze be a **Windows biztonság** párbeszédpanelére jelszóként.

      ![Az UNC-útvonal az Azure Files Csatlakozás oldaláról](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Megosztási pillanatkép létrehozása

Most, hogy leképezte a meghajtót, létrehozhat egy pillanatképet.

1. A portálon keresse meg a fájlmegosztást, és válassza a **Pillanatkép létrehozása lehetőséget.**

   ![Pillanatkép létrehozása](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. A virtuális gépben nyissa meg a *qstestfile.txt fájlt,* és írja be a "ez a fájl módosult" > mentse és zárja be a fájlt.
1. Hozzon létre egy másik pillanatképet.

## <a name="browse-a-share-snapshot"></a>Megosztáspillanatkép tallózása

1. A fájlmegosztáson válassza a **Pillanatképek megtekintése**lehetőséget.
1. A **Fájlmegosztás pillanatképei** ablaktáblán jelölje ki a lista első pillanatképét.

   ![Kijelölt pillanatkép az időbélyegek listájában](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. A pillanatkép ablaktábláján válassza a *qsTestFile.txt fájl*lehetőséget.

## <a name="restore-from-a-snapshot"></a>Visszaállítás pillanatképből

1. A fájlmegosztás pillanatképpaneljén kattintson a jobb gombbal a *qsTestFile fájlra*, és válassza a **Visszaállítás** gombot.
1. Válassza **az Eredeti fájl felülírása**lehetőséget.

   ![Letöltési és visszaállítási gombok](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. A virtuális gépben nyissa meg a fájlt. A nem módosított verzió visszaállításra került.

## <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése

1. A fájlmegosztáson válassza a **Pillanatképek megtekintése**lehetőséget.
1. A **Fájlmegosztás pillanatképei** ablaktáblán jelölje ki a lista utolsó pillanatképét, és kattintson a **Törlés gombra.**

   ![Törlés gomb](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Megosztáspillanatkép használata a Windowsban

Csakúgy, mint a helyszíni VSS-pillanatképek, megtekintheti a pillanatképeket a csatlakoztatott Azure-fájlmegosztás az Előző verziók lapon.

1. A Fájlkezelőben keresse meg a csatlakoztatott megosztást.

   ![Csatlakoztatott megosztás a Fájlkezelőben](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Válassza a *qsTestFile.txt* és > jobb gombbal parancsot, és válassza a menü **Tulajdonságok parancsát.**

   ![Kijelölt könyvtár helyi menüje](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Válassza az **Előző verziók** lehetőséget a könyvtár megosztási pillanatképeinek listázásához.

1. A pillanatkép megnyitásához válassza a **Megnyitás** lehetőséget.

   ![Előző verziók lap](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Visszaállítás korábbi verzióról

1. Válassza **a Visszaállítás**lehetőséget. Ez a művelet rekurzívmódon másolja a teljes könyvtár tartalmát az eredeti helyre a megosztási pillanatkép létrehozásakor.

   ![Visszaállítás gomb a](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png) figyelmeztető üzenetben Megjegyzés: Ha a fájl nem változott, akkor nem jelenik meg a fájl korábbi verziója, mert az a fájl ugyanaz, mint a pillanatkép. Ez összhangban van azzal, ahogyan ez a Windows fájlkiszolgálón működik.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure-fájlmegosztás használata Windows rendszeren](storage-how-to-use-files-windows.md)
