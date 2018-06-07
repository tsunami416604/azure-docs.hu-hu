---
title: A Tártallózó csatlakozni egy verem Azure-előfizetés vagy tárfiók |} Microsoft Docs
description: Kapcsolódás a Tártallózó verem Azure-előfizetéshez
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 9704f05cc6da97e33c0043b93acedc9e66bdcc36
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714901"
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription-or-a-storage-account"></a>A Tártallózó csatlakozni az Azure-verem előfizetéssel vagy a storage-fiók

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Ebből a cikkből megtudhatja, hogyan csatlakozni a veremben Azure-előfizetések és a Tártallózó alkalmazó tárfiókok. Az Azure storage explorer egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure verem storage-adatokkal Windows, a macOS és a Linux.

> [!NOTE]  
> Nincsenek elérhető áthelyezni az adatokat, és az Azure-verem tárolási eszközöket. További információkért lásd: [adatok át Azure verem tárolási eszközök](azure-stack-storage-transfer.md).

Ha még nem telepítette a Tártallózó még, [töltse le a Tártallózó](http://www.storageexplorer.com/) és telepítéséhez.

Miután kapcsolódott egy Azure verem előfizetés vagy tárfiók, használhatja a [az Azure storage explorer cikkek](../../vs-azure-tools-storage-manage-with-storage-explorer.md) az Azure-verem adatait. 

## <a name="prepare-for-connecting-to-azure-stack"></a>Kapcsolódás Azure verem előkészítése

Az Azure-verem vagy Tártallózó az Azure-verem előfizetés eléréséhez a VPN-kapcsolat közvetlen hozzáférés szükséges. A VPN-kapcsolat Azure Stackhez való beállításáról a [Csatlakozás az Azure Stackhez VPN segítségével](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) témakörben talál további információt.

Az Azure verem szoftverfejlesztői készlet kell exportálni a Azure verem legfelső szintű tanúsítványát.

### <a name="export-and-then-import-the-azure-stack-certificate"></a>Exportálja és importálja az Azure-verem tanúsítvány

1. Nyissa meg `mmc.exe` egy Azure verem gazdaszámítógépen, vagy a helyi virtuális gép Azure verem VPN-kapcsolatot. 

2. A **fájl**, jelölje be **beépülő modul hozzáadása/eltávolítása**, majd adja hozzá **tanúsítványok** kezeléséhez **a felhasználói fiók**.

3. A **konzol Root\Certificated (helyi számítógép) \Trusted Root Certification Authorities\Certificates** található **AzureStackSelfSignedRootCert**.

    ![Az Azure Stack főtanúsítványának betöltése az mmc.exe segítségével](./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png)

4. Kattintson a jobb gombbal a tanúsítvány, válasszon **feladataival** > **exportálása**, és kövesse az utasításokat a tanúsítvány exportálása **Base-64 kódolású X.509 (. CER)**.

    Az exportált tanúsítványt a következő lépésben fogja használni.

5. Indítsa el a tártallózót, és ha megjelenik a **csatlakozás az Azure Storage** párbeszédpanel mezőbe megszakítja a műveletet.

6. Az a **szerkesztése** menüben mutasson a **SSL-tanúsítványok**, majd válassza ki **importálási tanúsítványok**. A fájlválasztó párbeszédpanellel keresse meg és nyissa meg az előző lépésben exportált tanúsítványt.

    A tanúsítvány importálása, után felkéri indítsa újra a Tártallózó alkalmazással.

    ![Importálja a Tártallózó alkalmazással](./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png)

7. A Tártallózó újraindítása után válassza ki a **szerkesztése** menüt, és ellenőrizze, hogy ha **cél Azure-verem** van kiválasztva. Ha nem, válassza ki a **cél Azure verem**, majd indítsa újra a Tártallózó a változtatás érvénybe léptetéséhez. Ez a konfiguráció szükséges az Azure Stack környezettel való kompatibilitáshoz.

    ![Győződjön meg arról, hogy be van jelölve a Cél Azure Stack.](./media/azure-stack-storage-connect-se/target-azure-stack.png)

## <a name="connect-to-an-azure-stack-subscription"></a>Csatlakozás Azure Stack-előfizetéshez

Az alábbi lépések segítségével Tártallózó csatlakozás verem Azure-előfizetéshez.

1. A Tártallózó bal oldali ablaktáblában jelölje ki **fiókok kezelése**. 
    Ön bejelentkezett az összes Microsoft-előfizetés jelennek meg.

2. Csatlakozni a veremben Azure-előfizetéshez, válasszon **vegyen fel egy fiókot**.

    ![Azure Stack-fiók hozzáadása](./media/azure-stack-storage-connect-se/add-azure-stack-account.png)

3. A csatlakozás Azure Storage párbeszédpanelen alatt **Azure környezetben**, jelölje be **Azure** vagy **Azure Kína**, függ a verem Azure-fiókra, amely használatban van, jelölje be **Bejelentkezés** a legalább egy aktív Azure-verem előfizetéshez tartozó Azure verem fiókkal bejelentkezni.

    ![Csatlakozás az Azure Storage-hoz](./media/azure-stack-storage-connect-se/azure-stack-connect-to-storage.png)

4. Amint sikeresen bejelentkezett egy Azure Stack-fiókkal, a bal oldali ablaktáblán megjelenik a fiókhoz társított összes Azure Stack-előfizetés. Válassza ki azt az Azure Stack-előfizetést, amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget. (Az **Összes előfizetés** kiválasztásával vagy törlésével kijelölheti az összes felsorolt Azure Stack-előfizetést, vagy törölheti mindegyik jelölését.)

    ![Az Egyéni felhőkörnyezet párbeszédpanel mezőinek kitöltése után válassza ki az Azure Stack-előfizetéseket](./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png)

    A bal oldali ablaktábla megjeleníti a kiválasztott Azure Stack-előfizetésekhez társított összes tárfiókot.

    ![A tárfiókok listája, benne az Azure Stack-előfizetéssel rendelkező fiókokkal](./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png)

## <a name="connect-to-an-azure-stack-storage-account"></a>Csatlakozni a veremben Azure storage-fiók

Egy Azure verem storage-fiók használata a tárfiók neve vagy a kulcspár is kapcsolódhat.

1. A Tártallózó bal oldali ablaktáblában jelölje ki a fiókok kezelése. Ön bejelentkezett az összes Microsoft-fiókok jelennek meg.

    ![Fiók hozzáadása](./media/azure-stack-storage-connect-se/azure-stack-sub-add-an-account.png)

2. Csatlakozni a veremben Azure-előfizetéshez, válasszon **vegyen fel egy fiókot**.

    ![Fiók hozzáadása](./media/azure-stack-storage-connect-se/azure-stack-use-a-storage-and-key.png)

3. A csatlakozás az Azure Storage párbeszédpanel megnyitásához, válassza ki a **használja a tárfiók nevét és a kulcs**.

4. Adjon meg a fiók nevét a **fióknév**, illessze be a fiókkulcs a **fiókkulcs** szövegmezőben, jelölje be **más (írja be alább a)** a **tárolási végpontok tartomány** és az Azure-verem végpont bemeneti.

    A végpont Azure verem tartalmazza a két részből áll: egy régiót és az Azure-verem tartomány nevét. A csomagban Azure verem, az alapértelmezett végpont esetében **local.azurestack.external**. Ha nem biztos a végpont kapcsolatban, forduljon a felhő rendszergazdájához.

    ![Név és kulcs csatolása](./media/azure-stack-storage-connect-se/azure-stack-attach-name-and-key.png)

5. Kattintson a **Csatlakozás** gombra.
6. Miután a tárfiók sikeresen csatlakozik, a tárfiók jelenik meg, amely (**külső, más**) a névhez.

    ![VMWINDISK](./media/azure-stack-storage-connect-se/azure-stack-vmwindisk.png)

## <a name="next-steps"></a>További lépések

* [Ismerkedés a Tártallózó alkalmazással](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Verem az Azure storage: különbségek és szempontok](azure-stack-acs-differences.md)
* Az Azure storage kapcsolatos további információkért lásd: [Microsoft Azure storage bemutatása](../../storage/common/storage-introduction.md)
