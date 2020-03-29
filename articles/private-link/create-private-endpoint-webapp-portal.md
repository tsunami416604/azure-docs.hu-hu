---
title: Csatlakozás privát módon egy webalkalmazáshoz az Azure Private Endpoint használatával
description: Csatlakozás privát módon egy webalkalmazáshoz az Azure Private Endpoint használatával
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287815"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Csatlakozás privát módon egy webalkalmazáshoz az Azure Private Endpoint használatával (előzetes verzió)

Az Azure Private Endpoint az Azure-beli privát kapcsolat alapvető építőköve. Ez lehetővé teszi, hogy privát módon csatlakozzon a Web Apphoz.
Ebben a rövid útmutatóban megtudhatja, hogyan telepíthet egy webalkalmazást a privát végpontdal, és hogyan csatlakozhat ehhez a webalkalmazáshoz egy virtuális gépről.

További információ: [Privát végpontok használata az Azure Web Apphoz][privatenedpointwebapp]című témakörben talál.

> [!Note]
>Az előzetes verzió az USA keleti régiójában és az USA nyugati régiójában 2 régióban érhető el az összes PremiumV2 Windows és Linux web alkalmazáshoz. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="virtual-network-and-virtual-machine"></a>Virtuális hálózat és virtuális gép

Ebben a szakaszban létre fogja hozni a virtuális hálózatot és az alhálózatot a webalkalmazás privát végponton keresztül történő eléréséhez használt virtuális gép üzemeltetéséhez.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban virtuális hálózatot és alhálózatot hozhat létre.

1.  > A képernyő bal felső részén válassza az Erőforrás**létrehozása virtuális** > **hálózat** **létrehozása lehetőséget,** vagy keressen virtuális **hálózatot** a keresőmezőben.

1. A **Virtuális hálózat létrehozása**csoportban írja be vagy jelölje ki ezt az információt az Alapok lapon:

   > [!div class="mx-imgBorder"]
   > ![Virtuális hálózat létrehozása][1]

1. Kattintson **a "Tovább: AZ IP-címek >" elemre,** és adja meg vagy válassza ki ezt az információt:

   > [!div class="mx-imgBorder"]
   >![IP-címek konfigurálása][2]

1. Az alhálózat szakaszban kattintson a **"+ Alhálózat hozzáadása" gombra,** írja be a következő adatokat, majd kattintson a **"Hozzáadás" gombra.**

   > [!div class="mx-imgBorder"]
   >![Alhálózat hozzáadása][3]

1. Kattintson **a "Review + create"**

1. Miután az érvényesítés átment, kattintson a **"Create"**

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Az Azure Portal képernyőjének bal felső részén válassza az **Erőforrás** > **számítási** > **virtuális gép** létrehozása lehetőséget.

1. A Virtuális gép létrehozása – Alapismeretek csoportban adja meg vagy jelölje ki ezt az információt:

   > [!div class="mx-imgBorder"]
   >![Virtuális gép alapgépe][4]

1. Válassza a **"Tovább: Lemezek" lehetőséget**

   Tartsa meg az alapértelmezett beállításokat.

1. Válassza a **"Tovább: Hálózat"** lehetőséget, és válassza ezt az információt:

   > [!div class="mx-imgBorder"]
   >![Hálózatkezelés ][5]

1. Kattintson **a "Véleményezés + Létrehozás" lehetőségre**

1. Amikor az érvényesítés átadott üzenet, kattintson a **"Create"**

## <a name="create-your-web-app-and-private-endpoint"></a>A webalkalmazás és a privát végpont létrehozása

Ebben a szakaszban egy privát webalkalmazást hoz létre egy privát végpont használatával.

> [!Note]
>A privát végpont funkció csak a prémium v2 termékváltozathoz érhető el.

### <a name="web-app"></a>Webalkalmazás

1. Az Azure Portal képernyőjének bal felső részén válassza az **Erőforrás** > létrehozása**webweb** > **app**

1. A Webapp létrehozása – Alapjai mezőbe írja be vagy jelölje ki ezt az információt:

   > [!div class="mx-imgBorder"]
   >![A Web App alap][6]

1. Válassza a **"Véleményezés + létrehozás" lehetőséget**

1. Amikor az érvényesítés átadott üzenet, kattintson a **"Create"**

### <a name="create-the-private-endpoint"></a>A privát végpont létrehozása

1. A Web App tulajdonságai ban válassza a **Beállítások** > **hálózat lehetőséget,** és kattintson a **"Saját végpontkapcsolatok konfigurálása" lehetőségre.**

   > [!div class="mx-imgBorder"]
   >![Web App-hálózat][7]

1. A varázslóban kattintson a **"+ hozzáadás" gombra.**

   > [!div class="mx-imgBorder"]
   >![Web App privát végpontja][8]

1. Töltse ki az előfizetést, a virtuális hálózatot és az alhálózatadatait, és kattintson az **"OK"** gombra

   > [!div class="mx-imgBorder"]
   >![Webapp-hálózat][9]

1. A privát végpont létrehozásának áttekintése

   > [!div class="mx-imgBorder"]
   >![A][10]
   >![privát végpont végső nézetének áttekintése][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

1. A portál keresősávján adja meg a **myVm**
1. Válassza a **Csatlakozás gombot**. A Csatlakozás gomb kiválasztása után megnyílik a Csatlakozás a virtuális géphez lehetőséget, és válassza az **RDP**

   > [!div class="mx-imgBorder"]
   >![RDP gomb][12]

1. Az Azure létrehoz egy Távoli asztali protokoll (.rdp) fájlt, és letölti azt a számítógépre, miután **rákattintaz RDP-fájl letöltése**

   > [!div class="mx-imgBorder"]
   >![RDP-fájl letöltése][13]

1. Nyissa meg a downloaded.rdp fájlt.

- Ha a rendszer kéri, válassza a Csatlakozás lehetőséget.
- Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

> [!Note]
> Előfordulhat, hogy további lehetőségek et kell választania, > Másik fiók használata a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához.

- Kattintson az OK gombra.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha tanúsítványfigyelmeztetést kap, válassza az Igen vagy a Folytatás lehetőséget.

1. Miután megjelenik a virtuális gép asztala, minimalizálja azt, hogy visszatérjen a helyi asztalra.

## <a name="access-web-app-privately-from-the-vm"></a>Access Web App privát a virtuális gépről

Ebben a szakaszban privát módon fog csatlakozni a webalkalmazáshoz a privát végpont használatával.

1. A privát végpont privát IP-címének beszerezése a **keresősávprivát hivatkozás**mezőjében, és válassza a Privát hivatkozás lehetőséget.

   > [!div class="mx-imgBorder"]
   >![Privát kapcsolat][14]

1. A Privát kapcsolati központban válassza a **Privát végpontok** lehetőséget az összes privát végpont listázásához

   > [!div class="mx-imgBorder"]
   >![Privát kapcsolati központ][15]

1. A webalkalmazásra és az alhálózatra mutató privát végponthivatkozás kijelölése

   > [!div class="mx-imgBorder"]
   >![Privát végpont tulajdonságai][16]

1. A privát végpont privát IP-címének és a webalkalmazás teljes tartománynevének másolása a mi esetünkben a webappdemope.azurewebsites.net 10.10.2.4

1. A myVM-ben ellenőrizze, hogy a webalkalmazás nem érhető-e el a nyilvános IP-címen keresztül. Nyisson meg egy böngészőt, és illessze be a Web App nevét, rendelkeznie kell egy 403 tiltott hiba lap

   > [!div class="mx-imgBorder"]
   >![Forbidden][17]

> [!Important]
> Mivel ez a szolgáltatás előzetes verzióban érhető el, manuálisan kell kezelnie a DNS-bejegyzést.

1. A gazdagépbejegyzés létrehozása, a fájlkezelő megnyitása és a hosts fájl megkeresése

   > [!div class="mx-imgBorder"]
   >![Hosts fájl][18]

1. Bejegyzés hozzáadása a webalkalmazás privát IP-címével és nyilvános nevével a hosts fájl jegyzettömbbel történő szerkesztésével

   > [!div class="mx-imgBorder"]
   >![Tartalom gazdagépe][19]

1. Mentse a fájlt.

1. Nyisson meg egy böngészőt, és írja be a webalkalmazás URL-címét

   > [!div class="mx-imgBorder"]
   >![Webhely PE][20]

1. A webalkalmazást a Privát végponton keresztül éri el

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a privát végpont, a Web App és a virtuális gép használatával, törölje az erőforráscsoportot és az összes benne lévő erőforrást:

1. Írja be a ready-rg értéket a portál tetején lévő keresőmezőbe, és válassza a ready-rg lehetőséget a keresési eredmények közül.
1. Válassza az Erőforráscsoport törlése elemet.
1. Írja be a ready-rg parancsot az ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSához, és válassza a Törlés gombot.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban virtuális gépet hozott létre egy virtuális hálózaton, egy webalkalmazáson és egy privát végponton. Az internetről csatlakozott egy virtuális géphez, és a Privát hivatkozás használatával biztonságosan kommunikált a webalkalmazással. A Privát végpontról a [Mi az Azure-beli privát végpont.][privateendpoint]

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
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

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
