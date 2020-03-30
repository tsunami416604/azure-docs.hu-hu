---
title: Webalkalmazás létrehozása az ASE 1-es v1-es ében
description: Ismerje meg, hogyan hozhat létre webalkalmazásokat az App Service-környezet 1-es alkalmazásában. Ez a dokumentum csak az örökölt v1 ASE-t használó ügyfelek számára érhető el.
author: ccompy
ms.assetid: 983ba055-e9e4-495a-9342-fd3708dcc9ac
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 5c947617f0c27708e72f9bff92e2b0041473cd92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266194"
---
# <a name="create-a-web-app-in-an-app-service-environment-v1"></a>Webalkalmazás létrehozása az App Service-környezet 1-es alkalmazásában

> [!NOTE]
> Ez a cikk az App Service-környezet 1-es v1-es programjáról szól.  Az App Service-környezet egy újabb verziója könnyebben használható, és hatékonyabb infrastruktúrán fut. Ha többet szeretne megtudni az új verzió kezdődik az [App Service-környezet bemutatása](intro.md).
> 

## <a name="overview"></a>Áttekintés
Ez az oktatóanyag bemutatja, hogyan hozhat létre webalkalmazásokat és App Service-csomagokat egy [App Service-környezet v1](app-service-app-service-environment-intro.md) (ASE) alkalmazásban. 

> [!NOTE]
> Ha meg szeretné tudni, hogyan hozhat létre webalkalmazást, de nem kell azt egy App Service-környezetben megtennie, olvassa [el a .NET webalkalmazás létrehozása](../app-service-web-get-started-dotnet.md) vagy más nyelvekhez és keretrendszerekhez kapcsolódó oktatóanyagok egyikének című témakört.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy létrehozott egy App Service-környezetet. Ha még nem tette meg, olvassa el [az App Service-környezet létrehozása című témakört.](app-service-web-how-to-create-an-app-service-environment.md) 

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása
1. Az [Azure Portalon](https://portal.azure.com/)kattintson az **Erőforrás létrehozása > Web + Mobile > Web App**elemre. 
   
    ![][1]
2. Válassza ki előfizetését.  
   
    Ha több előfizetéssel is rendelkezik, vegye figyelembe, hogy az Alkalmazásszolgáltatás-környezetben egy alkalmazás létrehozásához ugyanazt az előfizetést kell használnia, amelyet a környezet létrehozásakor használt. 
3. Válasszon ki vagy hozzon létre egy erőforráscsoportot.
   
    *Az erőforráscsoportok* lehetővé teszik a kapcsolódó Azure-erőforrások egységként történő kezelését, és hasznosak az alkalmazások *szerepköralapú hozzáférés-vezérlési* (RBAC) szabályainak létrehozásakor. További információk: [Azure Resource Manager overview][ResourceGroups] (Az Azure Resource Manager áttekintése). 
4. Válassza ki vagy hozzon létre egy App Service-csomagot.
   
    *Az App Service-csomagok* webalkalmazások felügyelt készletei.  Általában az árképzés kiválasztásakor a felszámított ár az App Service-csomagra vonatkozik, nem pedig az egyes alkalmazásokra. Egy ASE fizet a számítási példányok az ASE helyett, amit az ASP-ben felsorolt.  Egy webalkalmazás példányainak számának skálázására skálázhatja az App Service-csomag példányait, és hatással van az adott csomagösszes webalkalmazásra.  Egyes funkciók, például a helytárolóhelyek vagy a Virtuálishálózat-integráció is mennyiségi korlátozásokat tartalmaznak a terven belül.  További információ: [Azure App Service-csomagok – áttekintés](../overview-hosting-plans.md)
   
    Az App Service-csomagok az ASE-ben a terv neve alatt feljegyzett hely megtekintésével.  
   
    ![][5]
   
    Ha olyan App Service-csomagot szeretne használni, amely már létezik az App Service-környezetben, válassza ki azt a csomagot. Ha új App Service-csomagot szeretne létrehozni, tekintse meg az oktatóanyag következő szakaszát, az [App Service-csomag létrehozása appszolgáltatási környezetben című témakört.](#createplan)
5. Írja be a webalkalmazás nevét, majd kattintson a **Létrehozás gombra.** 
   
    Ha az ASE külső VIP-t használ, az ASE-ben lévő alkalmazás URL-címe: [*sitename*]. [*az App Service-környezet neve*]. p.azurewebsites.net a [*helynév*helyett ].azurewebsites.net
   
    Ha az ASE belső VIP-t használ, akkor az adott ASE-ben lévő alkalmazás URL-címe: [*sitename*]. [*az ASE létrehozása során megadott altartomány*]   
    Miután kiválasztotta az ASP-t az ASE létrehozása során, látni fogja az altartomány frissítését a **Név alatt**

## <a name="create-an-app-service-plan"></a><a name="createplan"></a>App Szolgáltatási csomag létrehozása
Amikor egy App Service-csomagot hoz létre egy App Service-környezetben, a dolgozói lehetőségek eltérőek, mivel nincsenek megosztott dolgozók az ASE-ben.  A dolgozók, amelyeket használnia kell azok, amelyeket az admin az ASE számára kiosztott.  Ez azt jelenti, hogy egy új terv létrehozásához több dolgozót kell hozzárendelni az ASE munkavégző készletéhez, mint a példányok száma az összes már az adott munkavégző készletben lévő összes tervben.  Ha nincs elég dolgozó az ASE munkavégző készletben a terv létrehozásához, az ASE-rendszergazdával együtt kell dolgoznia a hozzáadott.

Egy másik különbség az App Service-környezet által üzemeltetett App Service-csomagokkal az árképzési kiválasztás hiánya.  Ha egy App Service-környezetben fizet a rendszer által használt számítási erőforrások, és nem rendelkezik hozzáadott díjakat a tervek ebben a környezetben.  Általában, amikor létrehoz egy App Service-csomagot, kiválaszt egy díjszabási csomagot, amely meghatározza a számlázást.  Az App Service-környezet lényegében egy privát hely, ahol tartalmat hozhat létre.  Ön fizet a környezetért, és nem a fogadó a tartalmat.

Az alábbi utasítások bemutatják, hogyan hozhat létre egy App Service-csomagot egy webalkalmazás létrehozása közben, ahogy azt az oktatóanyag előző szakaszában ismerteti.

1. Kattintson az **Új létrehozása gombra** a tervkijelölési felhasználói felületen, és adja meg a terv nevét, ugyanúgy, ahogy az ASE-n kívül szokott.
2. Válassza ki a használni kívánt ASE-t a helyválasztóból.
   
    Mivel az App Service-környezet lényegében egy privát központi telepítési hely, a Hely csoportban jelenik meg. 
   
    ![][2]
   
    Miután kiválasztott egy ASE a helyválasztó, az App Service-csomag létrehozása felhasználói felület frissítések.  A hely most az ASE-rendszer és a régió nevét jeleníti meg, és az árképzési terv választóját egy feldolgozókészlet-választó váltja fel.  
   
    ![][3]

### <a name="selecting-a-worker-pool"></a>Dolgozókészlet kiválasztása
Általában az Azure App Service-ben és egy App Service-környezeten kívül 3 számítási méret érhető el egy dedikált árcsomag kiválasztásával.  Hasonló módon egy ASE legfeljebb 3 feldolgozói készletet határozhat meg, és megadhatja az adott munkavégző készlethez használt számítási méretet.  Ez azt jelenti, hogy az ASE bérlői számára az, hogy ahelyett, hogy az App Service-csomag számítási méretével rendelkező díjszabási csomagot választana ki, kiválaszthatja az úgynevezett *feldolgozókészletet.*  

A dolgozói készlet kiválasztása felhasználói felületen az adott munkavégző készlethez használt számítási méret látható a név alatt.  A rendelkezésre álló mennyiség arra utal, hogy hány számítási példány használható az adott készletben.  Előfordulhat, hogy a teljes készletnek több példánya van, mint ennek a számnak, de ez az érték egyszerűen arra utal, hogy hány nincs használatban.  Ha módosítania kell az App Service-környezetet további számítási erőforrások hozzáadásához, olvassa [el az App Service-környezet konfigurálása](app-service-web-configure-an-app-service-environment.md)című témakört.

![][4]

Ebben a példában csak két munkavégző készlet érhető el. Ennek az az oka, hogy az ASE-rendszergazda csak a két feldolgozókészlethez rendelt állomásokat.  A harmadik akkor jelenik meg, ha vannak virtuális gépek hozzárendelt.  

## <a name="after-web-app-creation"></a>A webalkalmazás létrehozása után
Van néhány szempont a webalkalmazások futtatásához és az App Service-csomagok kezeléséhez egy ASE-ben, amelyeket figyelembe kell venni.  

Amint azt korábban említettük, az ASE tulajdonosa felelős a rendszer méretéért, és ennek eredményeképpen ők is felelősek annak biztosításáért, hogy elegendő kapacitás álljon rendelkezésre a kívánt App Service-csomagok üzemeltetéséhez. Ha nincsenek elérhető dolgozók, nem fogja tudni létrehozni az App Service-csomagot.  Ez a webalkalmazás skálázása is igaz.  Ha több példányra van szüksége, akkor az App Service-környezet rendszergazdáját kell beszereznie további dolgozók hozzáadásához.

A webalkalmazás és az App Service-csomag létrehozása után célszerű felskálázni.  Egy ASE mindig szükség van legalább 2 példánya az App Service-csomag hibatűrést az alkalmazások.  Az App Service-csomag méretezése egy ASE-ben megegyezik az App Service-csomag felhasználói felületén a szokásos módon.  További információ a méretezésről, [webalkalmazások méretezése App Service-környezetben](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../overview-hosting-plans.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[ResourceGroups]: ../../azure-resource-manager/management/overview.md
[AzurePowershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
