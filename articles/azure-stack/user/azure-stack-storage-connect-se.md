---
title: "Csatlakozás a Tártallózó verem Azure-előfizetéshez"
description: "Kapcsolódás tárolási Exporer verem Azure-előfizetéshez"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/28/2018
ms.author: xiaofmao
ms.openlocfilehash: bad4b6b0d829fb68dc25e84406a453071a36476a
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>Csatlakozás a Tártallózó verem Azure-előfizetéshez

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure Tártallózó (előzetes verzió) egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure verem Storage-adatokkal Windows, a macOS és a Linux. Nincsenek áthelyezni az adatokat, és az Azure Storage-verem több eszközök véve. További információkért lásd: [adatok át Azure verem tárolási eszközök](azure-stack-storage-transfer.md).

Ebből a cikkből megismerheti, hogyan a verem Azure storage-fiókok Tártallózóval való kapcsolódáshoz. 

Ha még nem telepítette a Tártallózó még, [letöltése](http://www.storageexplorer.com/) és telepítse azt.

Miután csatlakozott az Azure-verem előfizetés, használhatja a [Azure Tártallózó cikkek](../../vs-azure-tools-storage-manage-with-storage-explorer.md) az Azure-verem adatait. 

## <a name="prepare-an-azure-stack-subscription"></a>Egy Azure verem előfizetés előkészítése

Hozzá kell férni az Azure-verem gazdaszámítógépen asztali vagy Tártallózó az Azure-verem előfizetés eléréséhez a VPN-kapcsolatot. A VPN-kapcsolat Azure Stackhez való beállításáról a [Csatlakozás az Azure Stackhez VPN segítségével](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) témakörben talál további információt.

Az Azure verem szoftverfejlesztői készlet kell exportálni a Azure verem legfelső szintű tanúsítványát.

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>A exportálása, majd az Azure-verem tanúsítvány importálása

1. Nyissa meg `mmc.exe` egy Azure verem gazdaszámítógépen, vagy a helyi virtuális gép Azure verem VPN-kapcsolatot. 

2. A **fájl**, jelölje be **beépülő modul hozzáadása/eltávolítása**, majd adja hozzá **tanúsítványok** kezeléséhez **a felhasználói fiók**.



3. A **konzol Root\Certificated (helyi számítógép) \Trusted Root Certification Authorities\Certificates** található **AzureStackSelfSignedRootCert**.

    ![Az Azure Stack főtanúsítványának betöltése az mmc.exe segítségével][25]

4. Kattintson a jobb gombbal a tanúsítvány, válasszon **feladataival** > **exportálása**, és kövesse az utasításokat a tanúsítvány exportálása **Base-64 kódolású X.509 (. CER)**.  

    Az exportált tanúsítványt a következő lépésben fogja használni.
5. Indítsa el a Tártallózót (előzetes verzió), és ha megjelenik a **csatlakozás az Azure Storage** párbeszédpanel mezőbe megszakítja a műveletet.

6. Az a **szerkesztése** menüben mutasson a **SSL-tanúsítványok**, és kattintson a **importálási tanúsítványok**. A fájlválasztó párbeszédpanellel keresse meg és nyissa meg az előző lépésben exportált tanúsítványt.

    Az importálás után a rendszer kérni fogja a Tártallózó újraindítására.

    ![A tanúsítvány importálása a Tártallózóba (előzetes verzió)][27]

Most már készen áll a Tártallózó verem Azure-előfizetéshez csatlakozni.

### <a name="to-connect-an-azure-stack-subscription"></a>Az Azure-verem előfizetéssel kapcsolódni


1. A Tártallózó (előzetes verzió) újraindítása után válassza ki a **Szerkesztés** menüt, és győződjön meg arról, hogy be van jelölve a **Cél Azure Stack** jelölőnégyzet. Ha nincs bejelölve, jelölje be, és indítsa újra a Tártallózót a módosítás érvénybe léptetéséhez. Ez a konfiguráció szükséges az Azure Stack környezettel való kompatibilitáshoz.

    ![Győződjön meg arról, hogy be van jelölve a Cél Azure Stack.][28]

7. A bal oldali panelen válassza az **Fiókok kezelése** lehetőséget.  
    Megjelenik az összes Microsoft-fiók, amelybe bejelentkezett.

8. Az Azure Stack-fiókhoz való csatlakozáshoz kattintson a **Fiók hozzáadása** elemre.

    ![Azure Stack-fiók hozzáadása][29]

9. Az a **csatlakozás az Azure Storage** párbeszédpanel **Azure környezetben**, jelölje be **használata Azure verem környezet**, és kattintson a **tovább**.

10. Az Azure-verem fiókkal bejelentkezni Azure verem legalább egy aktív előfizetéssel társított, töltse ki a **jelentkezzen be Azure verem környezet** párbeszédpanel megnyitásához.  

    A mezők részletei az alábbiak:

    * **Környezetnév**: Ezt a mezőt a felhasználó testreszabhatja.
    * **ARM erőforrás végpontja**: Az Azure Resource Manager-erőforrás végpontjának mintái:

        * A felhő üzemeltetője:<br> https://adminmanagement.local.azurestack.external   
        * Bérlő:<br> https://management.local.azurestack.external
 
    * **A bérlői azonosító**: nem kötelező. Ezt az értéket csak akkor kell megadni, ha meg kell határozni a könyvtárat.

12. Amint sikeresen bejelentkezett egy Azure Stack-fiókkal, a bal oldali ablaktáblán megjelenik a fiókhoz társított összes Azure Stack-előfizetés. Válassza ki azt az Azure Stack-előfizetést, amellyel dolgozni szeretne, majd válassza az **Alkalmaz** lehetőséget. (Az **Összes előfizetés** kiválasztásával vagy törlésével kijelölheti az összes felsorolt Azure Stack-előfizetést, vagy törölheti mindegyik jelölését.)

    ![Az Egyéni felhőkörnyezet párbeszédpanel mezőinek kitöltése után válassza ki az Azure Stack-előfizetéseket][30]  
    A bal oldali ablaktábla megjeleníti a kiválasztott Azure Stack-előfizetésekhez társított összes tárfiókot.

    ![A tárfiókok listája, benne az Azure Stack-előfizetéssel rendelkező fiókokkal][31]

## <a name="next-steps"></a>További lépések
* [Ismerkedés a Tártallózó (előzetes verzió)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Verem az Azure Storage: különbségek és szempontok](azure-stack-acs-differences.md)


* Azure Storage kapcsolatos további információkért lásd: [Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md)

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
