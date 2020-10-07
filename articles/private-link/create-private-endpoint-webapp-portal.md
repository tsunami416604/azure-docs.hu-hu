---
title: Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával
description: Ez a cikk azt ismerteti, hogyan csatlakozhat egy webalkalmazáshoz az Azure Private Endpoint használatával.
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 10/07/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 3e0f05240aba9b5c92689315e409aaabe793b3f4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91772838"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint"></a>Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával

Az Azure Private-végpont az Azure privát kapcsolatának alapvető építőeleme. Privát végpont használatával a webalkalmazáshoz is csatlakozhat. Ebből a cikkből megtudhatja, hogyan helyezhet üzembe egy webalkalmazást privát végpont használatával, majd hogyan csatlakozhat a webalkalmazáshoz egy virtuális gépről (VM).

További információ: [privát végpontok használata egy Azure-webalkalmazáshoz][privateendpointwebapp].

> [!Note]
> A privát végpontok nyilvános régiókban érhetők el a PremiumV2, a PremiumV3 Windows Web Apps, a Linux Web Apps és a Azure Functions Premium csomag (más néven a rugalmas Prémium csomag) számára. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Mielőtt elkezdené, jelentkezzen be [a Azure Portalba](https://portal.azure.com).

## <a name="create-a-virtual-network-and-virtual-machine"></a>Virtuális hálózat és virtuális gép létrehozása

Ebben a szakaszban egy virtuális hálózatot és alhálózatot hoz létre egy olyan virtuális gép üzemeltetéséhez, amelyet egy webalkalmazás privát végponton keresztüli eléréséhez fog használni.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

A virtuális hálózat és az alhálózat létrehozásához tegye a következőket:

1. A bal oldali panelen válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **virtuális hálózat**lehetőséget.

1. A **virtuális hálózat létrehozása** panelen válassza az **alapok** lapot, majd adja meg az itt látható adatokat:

   > [!div class="mx-imgBorder"]
   > ![Képernyőfelvétel: a Azure Portal "Create Virtual Network" panelje.][1]

1. Válassza az **IP-címek** fület, majd adja meg az itt látható adatokat:

   > [!div class="mx-imgBorder"]
   > ![A virtuális hálózat létrehozása panel "IP-címek" lapjának képernyőképe.][2]

1. Az **alhálózat** szakaszban válassza az **alhálózat hozzáadása**lehetőséget, adja meg az itt látható adatokat, majd kattintson a **Hozzáadás**gombra.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép az "alhálózat hozzáadása" panelről.][3]

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

1. A sikeres ellenőrzés után válassza a **Létrehozás**lehetőséget.

### <a name="create-the-virtual-machine"></a>A virtuális gép létrehozása

A virtuális gép létrehozásához tegye a következőket:

1. A Azure Portal bal oldali ablaktábláján válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép**lehetőséget.

1. A **virtuális gép létrehozása – alapvető beállítások** panelen adja meg az itt látható adatokat:

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a "virtuális gép létrehozása" panelről.][4]

1. Válassza a **Tovább: lemezek**lehetőséget.

1. A **lemezek** ablaktáblán tartsa meg az alapértelmezett beállításokat, majd kattintson a **Tovább gombra: hálózatkezelés**elemre.

1. A **hálózatkezelés** ablaktáblán adja meg az itt látható adatokat:

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a "virtuális gép létrehozása" panel "hálózatkezelés" lapjáról.][5]

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

1. A sikeres ellenőrzés után válassza a **Létrehozás**lehetőséget.

## <a name="create-a-web-app-and-a-private-endpoint"></a>Webalkalmazás és privát végpont létrehozása

Ebben a szakaszban egy privát végpontot használó privát webalkalmazást hoz létre.

> [!Note]
> A privát végpont funkció csak a PremiumV2 és a PremiumV3 szinten érhető el.

### <a name="create-the-web-app"></a>A webapp létrehozása

1. A Azure Portal bal oldali ablaktábláján válassza az **erőforrás létrehozása**  >  **webes**  >  **webes alkalmazás**lehetőséget.

1. A **webalkalmazás** ablaktáblán válassza az **alapok** fület, majd adja meg az itt látható adatokat:

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a "Web App" panel "alapismeretek" lapjáról.][6]

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

1. A sikeres ellenőrzés után válassza a **Létrehozás**lehetőséget.

### <a name="create-the-private-endpoint"></a>A magánhálózati végpont létrehozása

1. A webalkalmazás tulajdonságai között, a **Beállítások**területen válassza a **hálózatkezelés**lehetőséget, majd a * * privát végponti kapcsolatok * * területen válassza **a privát végponti kapcsolatok konfigurálása**lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a "privát végponti kapcsolatok konfigurálása" hivatkozásra a webalkalmazás hálózatkezelési paneljén.][7]

1. A **Private Endpoint Connections** varázslóban válassza a **Hozzáadás**lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Képernyőfelvétel a "Private Endpoint connections" varázslóban a Hozzáadás gombra kattintva.][8]

1. Válassza ki a megfelelő információkat az **előfizetés**, a **virtuális hálózat**és az **alhálózat** legördülő listájában, majd kattintson az **OK gombra**.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a "privát végpont hozzáadása" panelről.][9]

1. A magánhálózati végpont létrehozásának előrehaladásának figyelése.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a privát végpont hozzáadásának folyamatáról. ][10]
   >  ![ Képernyőkép az újonnan létrehozott privát végpontról.][11]

## <a name="connect-to-the-vm-from-the-internet"></a>Kapcsolódás a virtuális géphez az internetről

1. A Azure Portal keresőmezőbe írja be a **MyVm** **kifejezést** .
1. Válassza a **kapcsolat**, majd az **RDP**lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép az "RDP" gombról a "myVM" panelen.][12]

1. A **kapcsolat RDP-vel** panelen válassza az **RDP-fájl letöltése**lehetőséget.  

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép az "RDP-fájl letöltése" gombra a "kapcsolat RDP-vel" panelen.][13]

   Az Azure létrehoz egy RDP protokoll (RDP) fájlt, és letölti a számítógépre.   

1. Nyissa meg a letöltött RDP-fájlt.

   a. A parancssorban válassza a **kapcsolat**lehetőséget.  
   b. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

     > [!Note]
     > A hitelesítő adatok használatához szükség lehet a **további lehetőségek**kiválasztására  >  **egy másik fiók használata**esetén.

1. Válassza az **OK** lehetőséget.

   > [!Note]
   > Ha a bejelentkezési folyamat során figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás**lehetőséget.

1. Amikor megjelenik a virtuális gép asztal ablaka, csökkentse a helyi asztalra való visszatéréshez.

## <a name="access-the-web-app-privately-from-the-vm"></a>Webes alkalmazás elérése a virtuális gépről

Ebben a szakaszban a privát végpont használatával kapcsolódhat a webalkalmazáshoz.

1. A privát végpont privát IP-címének lekéréséhez írja be a keresőmezőbe a **privát hivatkozás** **kifejezést** , majd az eredmények listájában válassza a **privát hivatkozás**lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a "Private link" hivatkozásról a keresési eredmények listájában.][14]

1. A Private link Center bal oldali ablaktábláján válassza a **privát végpontok** lehetőséget a privát végpontok megjelenítéséhez.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a privát végpontok listájáról a Private link Centerben.][15]

1. Válassza ki a webalkalmazásra és az alhálózatra mutató privát végpontot.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a privát végpont tulajdonságok paneljéről.][16]

1. Másolja a privát végpont magánhálózati IP-címét és a webalkalmazás teljes tartománynevét (FQDN). Az előző példában a privát azonosító a *`webappdemope.azurewebsites.net 10.10.2.4`* .

1. A **myVM** panelen ellenőrizze, hogy a webalkalmazás nem érhető el a nyilvános IP-címen keresztül. Ehhez nyisson meg egy böngészőt, és illessze be a webalkalmazás nevét. A lapon "hiba 403 – Tiltott" üzenetnek kell megjelennie.

   > [!div class="mx-imgBorder"]
   > ![Képernyőfelvétel: "hiba 403 – Tiltott" hibaüzenet.][17]

   A DNS esetében hajtsa végre az alábbi műveletek egyikét:
 
   - Használja a Azure DNS Private Zone szolgáltatást.  

     a. Hozzon létre egy nevű DNS-zónát *`privatelink.azurewebsites.net`* , majd kapcsolja össze a virtuális hálózattal.  
     b. Hozza létre a két rekordot (azaz az alkalmazás nevét és a szolgáltatásvezérlő Manager [SCM] nevet) a privát végpontjának IP-címével.  
     > [!div class="mx-imgBorder"]
     > ![Képernyőkép a saját DNS-zóna rekordjairól.][21]  

   - Használja a virtuális gép *hosts* fájlját.  

     a. Hozza létre a gazdagépek bejegyzést, nyissa meg a fájlkezelőt, és keresse meg a *hosts* fájlt.  
     > [!div class="mx-imgBorder"]
     > ![A Fájlkezelőben lévő hosts fájlt megjelenítő képernyőkép.][18]  
     b. Adjon hozzá egy olyan bejegyzést, amely a webalkalmazás magánhálózati IP-címét és nyilvános nevét tartalmazza a *hosts* fájl szövegszerkesztőben való szerkesztésével.  
     > [!div class="mx-imgBorder"]
     > ![Képernyőkép a gazdagépek fájljának szövegéről.][19]  
     c. Mentse a fájlt.

1. A böngészőben írja be a webalkalmazás URL-címét.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép egy webalkalmazást megjelenítő böngészőről.][20]

Most a privát végponton keresztül fér hozzá a webalkalmazáshoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a privát végponttal, a webalkalmazással és a virtuális géppel, törölje az erőforráscsoportot és a benne lévő összes erőforrást.

1. A Azure Portal a keresőmezőbe írja be a **Ready-RG** **kifejezést** , majd válassza a **Ready-RG** elemet az eredmények listájában.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. **A írja be az erőforráscsoport nevét**mezőbe írja be a **Ready-RG**értéket, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy virtuális GÉPET hozott létre egy virtuális hálózaton, egy webalkalmazáson és egy privát végponton. Csatlakozik egy virtuális géphez az internetről, és biztonságos módon kommunikál a webalkalmazással a privát hivatkozás használatával. 

További információ a privát végpontról: [Mi az az Azure Private Endpoint?][privateendpoint].

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
