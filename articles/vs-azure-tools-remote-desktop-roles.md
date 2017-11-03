---
title: "A távoli asztal használata Azure szerepkörök |} Microsoft Docs"
description: "A távoli asztal használata Azure szerepkörök"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: eab135d10c0d6df8ca72ac47d6804017a998a3d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-remote-desktop-with-azure-roles"></a>A távoli asztal használata Azure szerepkörök
Az Azure SDK-t és a távoli asztali szolgáltatások segítségével érheti el az Azure szerepkörök és az Azure által üzemeltetett virtuális gépeket. A Visual Studio a távoli asztali szolgáltatások egy Azure-felhőszolgáltatás-projekt t konfigurálhatja. Ahhoz, hogy a távoli asztali szolgáltatások, kell egy vagy több szerepkört tartalmazó működő projekt létrehozása és majd közzététele az Azure-bA.

> [!IMPORTANT]
> Hibaelhárítás vagy fejlesztői csak egy Azure szerepkörét hozzá kell férnie. Minden virtuális gép célja az Azure-alkalmazás nem más olyan ügyfélalkalmazásokhoz futtatásához egy adott szerepkör futtatásához. Ha Azure bármilyen célra használható virtuális gép üzemeltetésére használni kívánt, tekintse meg az Azure virtuális gépek használata a Server Explorer.
> 
> 

## <a name="to-enable-and-use-remote-desktop-for-an-azure-role"></a>Engedélyezése és távoli asztal használata az Azure-szerepkör
1. A Solution Explorerben nyissa meg a felhőszolgáltatás-projekt helyi menüjének, és válassza **közzététel**.
   
    A **Azure-alkalmazás közzététele** varázsló jelenik meg.
   
    ![A parancs egy Felhőszolgáltatás-projekt közzététele](./media/vs-azure-tools-remote-desktop-roles/IC799161.png)
2. Alján **Microsoft Azure közzétételi beállítási** lapján jelölje ki a **távoli asztal engedélyezése** az összes szerepkörök jelölőnégyzetet. 
   
    A **a távoli asztal konfigurálásának** párbeszédpanel jelenik meg.
3. Alján a **a távoli asztal konfigurálásának** párbeszédpanelen válassza ki a **további beállítások** gombra. 
   
    Megjelenik egy legördülő lista, amely lehetővé teszi, hogy hozzon létre vagy válasszon egy tanúsítványt, így amikor a távoli asztali kapcsolatra hitelesítő adatok titkosíthatók.
4. A legördülő listából válassza ki a  **&lt;létrehozás >**, vagy válasszon egy meglévőt a listából. 
   
    Ha úgy dönt, hogy a meglévő tanúsítványt, hagyja ki a következő lépéseket.
   
   > [!NOTE]
   > A tanúsítványokat, amelyekre szüksége van a távoli asztali kapcsolat eltérnek a többi Azure művelet használt tanúsítvány. A távelérési tanúsítványnak rendelkeznie kell egy titkos kulccsal.
   > 
   > 
   
    A **tanúsítvány létrehozása** párbeszédpanel jelenik meg.
   
   1. Adjon meg egy rövid nevet az új tanúsítvány számára, és válassza a **OK** gombra. Az új tanúsítvány megjelenik a legördülő listában.
   2. Az a **a távoli asztal konfigurálásának** párbeszédpanelen adja meg egy felhasználónevet és jelszót.
      
       Nem használhat egy meglévő fiókkal. Ne adja meg a rendszergazda az új fiók felhasználónevét.
      
      > [!NOTE]
      > Ha a jelszó nem felel meg az összetettségi követelményeknek, piros ikon jelenik meg a jelszó szövegmező mellett. A jelszónak kell tartalmaznia, nagybetűk, kisbetűk, és a számok vagy szimbólumok.
      > 
      > 
   3. Válassza ki a dátum a fiók jár és után, amely távoli asztali kapcsolatok le lesz tiltva.
   4. Miután a szükséges adatokat a megadott, válassza ki azt a **OK** gombra.
      
       Számos beállítás, amelyek lehetővé teszik a távelérés szolgáltatást a .cscfg és az .csdef fájlok kerülnek.
5. Az a **Microsoft Azure közzétételi beállítási** varázsló, válassza ki a **OK** gombra kattint, amikor készen áll a közzététele a felhőalapú szolgáltatás.
   
    Ha nem Ön elkészült, válassza ki a **Mégse** gombra. A konfigurációs beállítások lesznek mentve, és később közzététele a felhőalapú szolgáltatás.

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>A távoli asztal használatával csatlakoznak az Azure-szerepkör
Miutá közzéteszi az Azure felhőalapú szolgáltatás, a Server Explorer segítségével jelentkezzen be a virtuális gépek Azure futtató. 

1. A Server Explorer eszközben bontsa ki a **Azure** csomópontot, majd bontsa ki a felhőszolgáltatás és a szerepkörök és példányok jelenítse meg egyik csomópontját.
2. Nyissa meg a helyi menü egy példány csomópont, és válassza **csatlakozzon a távoli asztal**.
   
    ![Távoli asztali kapcsolatra](./media/vs-azure-tools-remote-desktop-roles/IC799162.png)
3. Adja meg a felhasználónevet és jelszót, amelyet korábban hozott létre. Most jelentkezett be a távoli munkamenethez.

