---
title: "A Mobile Services hozzáadása a kapcsolódó szolgáltatások a Visual Studio használatával |} Microsoft Docs"
description: "A Visual Studio kapcsolódó szolgáltatások hozzáadása párbeszédpanelen adja hozzá a Mobile Services"
services: visual-studio-online
documentationcenter: na
author: mlhoop
manager: douge
editor: 
ms.assetid: 75c3cb93-88e1-476d-a416-f34caa3608e3
ms.service: visual-studio-online
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: mobile
ms.date: 12/16/2015
ms.author: mlearned
ms.openlocfilehash: d185fdafebad56f8970e390b2a0672c3fb84df8f
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>A Mobile Services hozzáadása a Visual Studio kapcsolódó szolgáltatások használatával
A Visual Studio 2015-öt, Azure Mobile Services használatával csatlakozhat a **kapcsolódó szolgáltatás hozzáadása** párbeszédpanel. Bármely C# ügyfélalkalmazás, bármely JavaScript-alkalmazást, illetve a platformok közötti Cordova-alkalmazás lehet csatlakoztatni. Miután csatlakozott, létrehozása és adatok eléréséhez, hozzon létre egyéni API-k és ütemezett feladatok, vagy a leküldéses értesítések támogatását.  A kapcsolódó szolgáltatások művelet hozzáadja a megfelelő hivatkozások és a kapcsolat kódot. Is kihasználhatja a hitelesítés népszerű identitáskezelési rendszerek, például Azure AD számos beépített támogatása Facebook, a Twitter és a Microsoft Accounts.

## <a name="supported-project-types"></a>Támogatott projekttípusok
> [!NOTE]
> A Visual Studio 2015-öt Azure Mobile Services hozzáadása egy univerzális Windows (Windows 10) projektek a kapcsolódó szolgáltatások hozzáadása párbeszédpanelen nem támogatott. Azure Mobile Services telepítésével, a megfelelő csomagokat a projekthez a NuGet Package Manager használatával adhat hozzá.
> 
> 

A kapcsolódó szolgáltatások párbeszédpanel segítségével Azure Mobile Services kapcsolódni a következő projekt típusok.

* .NET Windows 8.1 Áruházbeli, telefon és univerzális alkalmazás projektek
* JavaScript Windows 8.1 Áruházbeli, telefon és univerzális alkalmazás projektek
* Az Apache Cordova Visual Studio eszközök használatával létrehozott projektek

## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Kapcsolódás az Azure Mobile Services használatával a kapcsolódó szolgáltatások hozzáadása párbeszédpanel
1. Győződjön meg arról, hogy az Azure-fiók. Ha az Azure-fiók nem rendelkezik, akkor regisztrálhatnak az egy [ingyenes próbaverzió](http://go.microsoft.com/fwlink/?LinkId=518146).
2. Nyissa meg a **kapcsolódó szolgáltatások hozzáadása** párbeszédpanel megnyitásához.
   
   * .NET-alkalmazások esetén nyissa meg a projektet a Visual Studio, a helyi menü megnyitása a **hivatkozások** csomópont a Megoldáskezelőben, majd válassza **kapcsolódó szolgáltatás hozzáadása**
     
        ![Csatlakozás Azure-Mobilszolgáltatásból](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)
   * Az Apache Cordova-alkalmazás projektet, nyissa meg a projektet a Visual Studio, a Solution Explorerben nyissa meg a projekt csomópontjának helyi menüjére, és válassza **kapcsolódó szolgáltatás hozzáadása**.
3. Az a **kapcsolódó szolgáltatás hozzáadása** párbeszédpanelen válassza ki **Azure Mobile Services**, majd válassza a **konfigurálása** gombra. Azure bejelentkezni, ha még nem tette kérheti.
   
    ![Egy Azure-mobilszolgáltatás hozzáadása](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)
4. Az a **Azure Mobile Services** párbeszédpanelen válasszon egy meglévő mobilszolgáltatás, ha rendelkezik ilyennel. Ha szeretne létrehozni egy új Azure mobilszolgáltatás, ehhez az alábbi eljárást követve. Egyéb esetben folytassa a következő lépéssel.
   
    Új mobilszolgáltatás-fiók létrehozása:
   
   1. Válassza ki a ** szolgáltatás létrehozása ** hivatkozást a párbeszédpanel alján.
       ![Új csatlakoztatott mobilszolgáltatás hozzáadása](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)
   2. Az a **Mobile szolgáltatás létrehozása** párbeszédpanelen kiválaszthatja a JavaScript háttérrendszer mobilszolgáltatás vagy .NET háttérrendszer mobilszolgáltatás a a **futásidejű** legördülő listából. 
      
       ![A mobilszolgáltatás létrehozása](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)
      
       A JavaScript háttérrendszer szolgáltatás egyszerű és hatékony. Ha a JavaScript háttérrendszer mobilszolgáltatások létrehozását, a kiszolgálóoldali JavaScript-kódot tárolja a felhőben, de server parancsfájlok szerkesztheti a Server Explorer vagy az Azure felügyeleti portálján. 
      
       A .NET-háttérrendszer mobil szolgáltatás lehetővé teszi a teljes hatékonyságát és rugalmasságát Web API és az Entity Framework. Ha a .NET háttérrendszer mobilszolgáltatások létrehozását, a projekt hozott létre az Ön és a megoldás adni. 
   3. Válassza ki a **régió** Ha szeretné, hogy a mobilszolgáltatás, és a kiszolgáló adja meg egy felhasználónevet és jelszót.
   4. Miután beírt a szükséges adatokat, válassza ki azt a **létrehozása** a mobilszolgáltatás létrehozása gombra.
   5. Az új mobilszolgáltatás a lista jelenjenek meg a **Azure Mobile Services** párbeszédpanel megnyitásához. Válassza ki az új mobilszolgáltatás a listában, és válassza ki a **Hozzáadás** gombra a szolgáltatás hozzáadása a projekthez.
5. Tekintse át az első lépések oldal jelenik meg, és megtudhatja, hogyan a projekthez lett módosítva. A böngészőben egy első lépések lap jelenik meg, amikor egy összekapcsolt szolgáltatás. Tekintse át a javasolt lépések és példák, vagy váltson a Mi történt a lapot, melyen megtekintheti, milyen hivatkozások lettek hozzáadva a projekthez, és hogyan módosultak a kódot és konfigurációs fájljait.
6. A Kódminták útmutatóként használva elkezd kódot, amely a mobilszolgáltatás!

## <a name="how-your-project-is-modified"></a>A projekt módosítása hogyan
A projekt típusától függ módját a Visual Studio módosítja a projekthez. C# ügyfélalkalmazások, lásd: [Mi történt – C# projektek](http://go.microsoft.com/fwlink/p/?LinkId=513119). JavaScript ügyfélalkalmazások, lásd: [Mi történt – a JavaScript-projektek](http://go.microsoft.com/fwlink/p/?LinkId=513120). Cordova-alkalmazásokkal, lásd: [Mi történt – a Cordova-projektek](http://go.microsoft.com/fwlink/p/?LinkId=513116).

## <a name="next-steps"></a>Következő lépések
Kérdései vannak, és rátalálhat: 

* [MSDN fórumon: Azure Mobile Services](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)
* [Azure Mobile Services, a Microsoft Azure-csapat blogja](https://azure.microsoft.com/blog/topics/mobile/)
* [Azure Mobile Services azure.microsoft.com:](https://azure.microsoft.com/services/mobile-services/)
* [Azure Mobile Services dokumentációját a azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)

