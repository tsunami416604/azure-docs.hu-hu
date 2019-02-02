---
title: A rövid útmutató létrehozása, és használata Azure-fájlmegosztások Windows |} A Microsoft Docs
description: Ez a rövid útmutató segítségével létrehozása és használata Azure-fájlmegosztások Windows.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: wgries
ms.component: files
ms.openlocfilehash: 141a8c9d63d3f0fd615ec0648b15c669f28f7118
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663995"
---
# <a name="quickstart-create-and-use-an-azure-file-share-for-windows"></a>Gyors útmutató: Létrehozása és használata Azure-fájlmegosztások Windows
A cikk létrehozása és használata az Azure-fájlmegosztások alapvető lépéseit mutatja be. Ez a rövid útmutatóban a hangsúly van gyorsan Azure-fájlmegosztás beállítása, a szolgáltatás működése tapasztal. Ha részletesebb utasításokat kell létrehozásához és az Azure-fájlmegosztások a saját környezetben, lásd: [használata a Windows Azure-fájlmegosztások](storage-how-to-use-files-windows.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="prepare-your-environment"></a>A környezet előkészítése
Egy Azure-fájlmegosztást hoz létre, mielőtt szüksége állítsa be a következő elemek esetében ez a rövid útmutató:

- Az Azure storage-fiók és a egy Azure-fájlmegosztás
- A Windows Server 2016 Datacenter VM

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Azure-fájlmegosztás használata előtt kell az Azure storage-fiók létrehozása. A tárfiókok az Azure-fájlmegosztások vagy más tárolási erőforrások, például blobok vagy üzenetsorok üzembe helyezéséhez használható tárolók közös készletei. A tárfiókok korlátlan számú megosztást tartalmazhatnak, Egy megosztás korlátlan számú fájl tárolására használható, egészen a tárfiók kapacitásának korlátjáig.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Utána a fájlmegosztás létrehozása következik.

1. Az Azure storage-fiók üzembe helyezésének befejeződése után válassza ki a **erőforrás megnyitása**.
1. Válassza ki **fájlok** a storage-fiók panelen.

    ![Fájlok kiválasztása](./media/storage-files-quick-create-use-windows/click-files.png)

1. Válassza ki **+ fájlmegosztás**.

    ![Válassza ki a fájlmegosztás hozzáadása gombra](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Nevezze el az Új fájlmegosztás *qsfileshare* > "1" adja meg a **kvóta** > Válasszon **létrehozás**. A kvóta legfeljebb 5 TiB lehet, de csak kell az 1 GiB ebben a rövid útmutatóban.
1. Hozzon létre egy új txt típusú fájlt *qsTestFile* a helyi gépen.
1. Válassza ki az új fájlmegosztást, majd válassza ki a fájlmegosztás helyét, **feltöltése**.

    ![Fájl feltöltése](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Keresse meg a helyet, ahol létrehozta a .txt fájlt > Válasszon *qsTestFile.txt* > Válasszon **feltöltése**.

Az eddigi Ezzel létrehozott egy Azure storage-fiókot és a egy fájlmegosztást, egy fájl Azure-ban. Ezután a Windows Server 2016 Datacenter, amelyek a helyszíni kiszolgáló ebben a rövid útmutatóban az Azure virtuális gép fog létrehozni.

### <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése
1. Ezután bontsa ki a menüt a portál bal oldalán, és válassza ki az **Erőforrás létrehozása** lehetőséget az Azure portál bal felső sarkában.
1. Az **Azure Marketplace**-erőforrások fölött lévő keresési mezőben keressen a **Windows Server 2016 Datacenter** elemre, majd válassza a **Létrehozás** lehetőséget.
1. Az a **alapjai** lap **projektadatok**, válassza ki az ebben a rövid útmutatóban létrehozott erőforráscsoportot.

   ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. A **példány részletei**, nevezze el a virtuális gép *qsVM*.
1. Hagyja bejelölve az alapértelmezett beállításokat a **Régió**, **Rendelkezésre állási beállítások**, **Kép** és **Méret** mezőkben.
1. Alatt **rendszergazdai fiók**, adjon hozzá *VMadmin* , a **felhasználónév** , és adja meg egy **jelszó** a virtuális gép számára.
1. A **Bejövőport-szabályok** területen válassza a **Kijelölt portok engedélyezése**lehetőséget, majd válassza az **RDP (3389)** és a **HTTP** elemeket a legördülő listából.
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Kattintson a **Létrehozás** gombra. Az új virtuális gép létrehozása eltarthat pár percig.

1. A virtuális gép üzembe helyezés befejezése után jelölje ki a **erőforrás megnyitása**.

Ezen a ponton már létrehozta az új virtuális gépet és csatolt egy adatlemezt. Most pedig csatlakoznunk kell a virtuális géphez.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

1. Válassza ki **Connect** a virtuális gép tulajdonságai lapon.

   ![Csatlakozás az Azure-beli virtuális gépekhez a portálról](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Az a **csatlakozhat a virtuális gép** lapon, a csatlakozás az alapértelmezett beállításokat tartsa **IP-cím** keresztül **portszám** *3389-es* , és válassza ki **Töltse le az RDP-fájl**.
1. Nyissa meg a letöltött RDP-fájlt, és válassza ki **Connect** amikor a rendszer kéri.
1. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet, *localhost\username*, ahol &lt;felhasználónév&gt; létrehozta a virtuális gép, a virtuális gép rendszergazdai felhasználóneve. Adja meg a jelszót, amelyet a virtuális gép, és válassza **OK**.

   ![További lehetőségek](./media/storage-files-quick-create-use-windows/local-host2.png)

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza ki **Igen** vagy **Folytatás** a kapcsolat létrehozásához.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Képezze le az Azure-fájlmegosztás egy Windows-meghajtóra

1. Az Azure Portalon lépjen a *qsfileshare* fileshare válassza **Connect**.
1. Másolja a második mező tartalmát, és illessze be a **Jegyzettömb**.

   ![Az UNC-útvonal az Azure Files Csatlakozás oldaláról](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. Nyissa meg a virtuális gép **fájlkezelő** válassza **Ez a gép** az ablakban. Ezzel a választással a szalagon elérhető menüket változik. Az a **számítógép** menüjében válassza **hálózati meghajtó csatlakoztatása**.
1. Válassza ki a meghajtó betűjelét, és írja be az UNC-útvonalat. Ha követte az elnevezési javaslatai ebben a rövid, másolja  *\\qsstorageacct.file.core.windows.net\qsfileshare* a **Jegyzettömb**.

   Győződjön meg arról, hogy a rendszer ellenőrzi a különül.

   ![A Hálózati meghajtó csatlakoztatása párbeszédpanel képernyőképe](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Válassza a **Finish** (Befejezés) elemet.
1. Az a **Windows biztonsági** párbeszédpanel:

   - A Jegyzettömb, másolja a tárfiók AZURE\ előtaggal kiegészített nevét, és illessze be a **Windows biztonsági** párbeszédpanel felhasználóneve. Ha követte az elnevezési javaslatai ebben a rövid, másolja *AZURE\qsstorageacct*.
   - A Jegyzettömb, másolja a tárfiók kulcsát, és illessze be a **Windows biztonsági** jelszóként párbeszédpanel bezárásához.

      ![Az UNC-útvonal az Azure Files Csatlakozás oldaláról](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Megosztási pillanatkép létrehozása
Most, hogy a meghajtó már csatlakoztatott, létrehozhat egy pillanatkép.

1. A portálon keresse meg a fájlmegosztás, és válassza **létrehozás pillanatkép**.

   ![Pillanatkép létrehozása](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. A virtuális Géphez, nyissa meg a *qstestfile.txt* , és írja be az "Ez a fájl módosult" > mentse és zárja be a fájlt.
1. Hozzon létre egy másik pillanatkép.

## <a name="browse-a-share-snapshot"></a>Keresse meg a megosztási pillanatképek

1. Válassza ki a fájlmegosztáson, **pillanatképek megtekintése**.
1. Az a **fájlmegosztás pillanatképeinek** ablaktáblán válassza ki az első pillanatképek a listában.

   ![Kijelölt pillanatkép időbélyegeket listájában](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. A pillanatkép panelen válassza ki a *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Visszaállítás pillanatképből

1. Válassza ki a portálról, *qsTestFile* > Válassza ki a **visszaállítása** gombra.
1. Válassza ki **eredeti fájl felülírása**.

   ![Töltse le és visszaállítási gombok](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. A virtuális Géphez nyissa meg a fájlt. A módosítás nélküli verzió vissza lett állítva.

## <a name="delete-a-share-snapshot"></a>Megosztási pillanatkép törlése

1. Válassza ki a fájlmegosztáson, **pillanatképek megtekintése**.
1. Az a **fájlmegosztás pillanatképeinek** ablaktábláján válassza ki a legutóbbi pillanatképet a listából, majd kattintson **törlése**.

   ![Törlés gomb](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Megosztási pillanatképek használata a Windows
Csakúgy, mint a helyi VSS-pillanatképekkel, megtekintheti a pillanatképek a csatlakoztatott Azure-fájlmegosztásról az előző verziók lap használatával.

1. A Fájlkezelőben keresse meg a csatlakoztatott megosztást.

   ![A Fájlkezelőben használt csatlakoztatott megosztási](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Válassza ki *qsTestFile.txt* és a > kattintson a jobb gombbal, és válassza ki **tulajdonságok** a menüből.

   ![Kijelölt könyvtár helyi menüje](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Válassza az **Előző verziók** lehetőséget a könyvtár megosztási pillanatképeinek listázásához.

1. Válassza ki **nyissa meg a** a pillanatkép megnyitásához.

   ![Előző verziók lap](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Visszaállítás korábbi verzióról

1. Válassza ki **visszaállítása**. Ez a művelet a tartalmát a teljes könyvtár rekurzív módon másolja az eredeti helyre a megosztási pillanatkép létrehozásának időpontjában.

   ![Gombra a figyelmeztető üzenetek visszaállítása](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure-fájlmegosztás használata Windows rendszeren](storage-how-to-use-files-windows.md)