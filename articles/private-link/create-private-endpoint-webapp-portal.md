---
title: Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával
description: Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint használatával
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: bb78536326885e043279de1ff77e6e8efcd95193
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037156"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Privát kapcsolódás egy webalkalmazáshoz az Azure Private Endpoint (előzetes verzió) használatával

Az Azure privát végpontja az Azure-beli privát kapcsolat alapvető építőeleme. Lehetővé teszi a webalkalmazáshoz való magánhálózati kapcsolódást.
Ebből a rövid útmutatóból megtudhatja, hogyan helyezhet üzembe egy webalkalmazást privát végponttal, és hogyan csatlakozhat ehhez a webalkalmazáshoz egy virtuális gépről.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="virtual-network-and-virtual-machine"></a>Virtuális hálózat és virtuális gép

Ebben a szakaszban a virtuális hálózatot és az alhálózatot fogja létrehozni a webalkalmazás privát végponton keresztüli eléréséhez használt virtuális gép üzemeltetéséhez.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózat** lehetőséget, vagy keressen rá a **virtuális hálózatra** a keresőmezőbe.

1. A **virtuális hálózat létrehozása**területen adja meg vagy válassza ki ezt az információt az alapok lapon:

 ![Virtual Network létrehozása][1]

1. Kattintson a **"tovább: IP-címek >"** elemre, és adja meg vagy válassza ki az alábbi adatokat:

![IP-címek konfigurálása][2]

1. Az alhálózat szakaszban kattintson az **"+ alhálózat hozzáadása"** elemre, és adja meg a következő információkat, majd kattintson a **"Hozzáadás"** gombra.

![Alhálózat hozzáadása][3]

1. Kattintson a **"felülvizsgálat + létrehozás"** gombra.

1. Az ellenőrzés után kattintson a **"létrehozás"** gombra.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása** > **számítási** > **virtuális gép** lehetőséget.

1. A virtuális gép létrehozása – alapismeretek területen adja meg vagy válassza ki az alábbi adatokat:

![Alapszintű virtuális gép ][4]

1. Válassza a **"tovább: lemezek" lehetőséget.**

Tartsa meg az alapértelmezett beállításokat.

1. Válassza a **"tovább: hálózatkezelés"** lehetőséget, majd válassza ki az alábbi adatokat:

![Hálózat ][5]

1. Kattintson a **"felülvizsgálat + létrehozás"** gombra.

1. Az érvényesítési üzenetben kattintson a **"létrehozás"** gombra.

## <a name="create-your-web-app-and-private-endpoint"></a>Webalkalmazás és privát végpont létrehozása

Ebben a szakaszban egy magánhálózati webalkalmazást hoz létre egy privát végpont használatával.

> [!Note]
>A privát végpont funkció csak a prémium v2-hez és a külső bevezetési SKU-hoz való elkülönítéshez érhető el

### <a name="web-app"></a>Webalkalmazás

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása** > **webes** > **webalkalmazás** lehetőséget.

1. A webalkalmazás létrehozása – alapok lapon adja meg vagy válassza ki az alábbi adatokat:

![Alapszintű Web App ][6]

1. Válassza a **"felülvizsgálat + létrehozás"** lehetőséget

1. Az érvényesítési üzenetben kattintson a **"létrehozás"** gombra.

### <a name="create-the-private-endpoint"></a>A magánhálózati végpont létrehozása

1. A webalkalmazás tulajdonságainál válassza a **beállítások** > **hálózatkezelés** lehetőséget, majd kattintson a **"privát végponti kapcsolatok konfigurálása"** elemre.

![Webalkalmazás hálózatkezelése][7]

1. A varázslóban kattintson a **"+ Hozzáadás"** gombra.

![Webalkalmazás magánhálózati végpontja][8]

1. Töltse ki az előfizetést, a vnet és az alhálózati információkat, majd kattintson **az OK** gombra.

![Webalkalmazás hálózatkezelése][9]

1. A magánhálózati végpont létrehozásának áttekintése

![tekintse át][10]
![a privát végpont végső nézetét][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Kapcsolódás virtuális géphez az internetről

1. A portál keresési sávján írja be a **myVm**
1. Kattintson a **kapcsolat gombra**. A kapcsolódás gombra kattintva megnyílik a virtuális gép, és válassza az **RDP** lehetőséget.

![RDP gomb][12]

1. Az Azure létrehoz egy RDP protokoll (. rdp) fájlt, és letölti azt a számítógépre, miután rákattint az **RDP-fájl letöltése** elemre.

![RDP-fájl letöltése][13]

1. Nyissa meg a letöltött. rdp fájlt.

- Ha a rendszer kéri, válassza a csatlakozás lehetőséget.
- Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

> [!Note]
> Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell kiválasztania > eltérő fiókot használjon.

- Kattintson az OK gombra.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az igen vagy a Folytatás lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.

## <a name="access-web-app-privately-from-the-vm"></a>Webes alkalmazás elérése a virtuális gépről

Ebben a szakaszban a privát végponton keresztül fog csatlakozni a webalkalmazáshoz.

1. A privát végpont magánhálózati IP-címének lekérése, a keresősáv Type **Private hivatkozása**, és válassza a privát hivatkozás lehetőséget.

![Privát kapcsolat][14]

1. A privát kapcsolat központban válassza a **privát végpontok** lehetőséget az összes privát végpont listázásához.

![Privát kapcsolati központ][15]

1. Válassza ki a webalkalmazáshoz és az alhálózathoz tartozó privát végpont hivatkozást

![Magánhálózati végpont tulajdonságai][16]

1. Másolja a saját privát végpontjának magánhálózati IP-címét és a webalkalmazás teljes tartománynevét a mi esetünkben webappdemope.azurewebsites.net 10.10.2.4

1. A myVM ellenőrizze, hogy a webalkalmazás nem érhető el a nyilvános IP-címen keresztül. Nyisson meg egy böngészőt, és másolja a webalkalmazás nevét, a 403-es tiltott hibaüzenet oldalát kell megadnia.

![Forbidden][17]

> [!Note]
> Mivel ez a funkció előzetes verzióban érhető el, manuálisan kell kezelnie a DNS-bejegyzést.

1. Hozza létre a gazdagép bejegyzését, nyissa meg a fájlkezelőt, és keresse meg a Hosts fájlt.

![Hosts fájl][18]

1. Bejegyzés hozzáadása a magánhálózati IP-címmel és a webalkalmazás nyilvános nevével a Hosts fájl Jegyzettömb használatával való szerkesztésével

![Gazdagépek tartalma][19]

1. Mentse a fájlt.

1. Nyisson meg egy böngészőt, és írja be a webalkalmazás URL-címét

![Webhely PE-vel][20]

1. A privát végponton keresztül fér hozzá a webalkalmazáshoz

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a privát végponttal, a webalkalmazással és a virtuális géppel, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a Ready-RG kifejezést a portál felső részén található keresőmezőbe, és válassza a Ready-RG lehetőséget a keresési eredmények közül.
1. Válassza az erőforráscsoport törlése lehetőséget.
1. Írja be a Ready-RG értéket az ERŐFORRÁSCSOPORT nevének beírásához, majd válassza a Törlés lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy virtuális GÉPET egy virtuális hálózaton, egy webalkalmazáson és egy privát végponton. Csatlakozik egy virtuális géphez az internetről, és biztonságosan kommunikál a webalkalmazással a privát hivatkozás használatával. További információ a privát végpontról: [Mi az az Azure Private Endpoint][privateendpoint].

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
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
