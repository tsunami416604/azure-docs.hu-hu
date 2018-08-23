---
title: Mobilszolgáltatások hozzáadása a Visual Studio csatlakoztatott szolgáltatásai segítségével |} A Microsoft Docs
description: Adja hozzá a Mobile Services használatával a Visual Studio csatlakoztatott szolgáltatás hozzáadása párbeszédpanel
services: visual-studio-online
documentationcenter: na
author: mlhoop
manager: douge
editor: ''
ms.assetid: 75c3cb93-88e1-476d-a416-f34caa3608e3
ms.service: visual-studio-online
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: mobile
ms.custom: vs-azure
ms.date: 12/16/2015
ms.author: mlearned
ms.openlocfilehash: 6cceb760ab62ea4f7225c9cc0cee7a6baf8ed8be
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42442211"
---
# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Mobilszolgáltatások hozzáadása a Visual Studio csatlakoztatott szolgáltatásain használatával
A Visual Studio 2015, Azure Mobile Services használatával csatlakozhat a **csatlakoztatott szolgáltatás hozzáadása** párbeszédpanel. Minden olyan C# ügyfélalkalmazás, bármely JavaScript-alkalmazást vagy a platformfüggetlen Cordova-alkalmazáshoz csatlakozhat. Miután csatlakoztatta, létrehozása és adatok eléréséhez, hozzon létre egyéni API-k és az ütemezett feladatok, vagy adja hozzá a leküldéses értesítések támogatását.  A csatlakoztatott szolgáltatások művelet hozzáadja a megfelelő hivatkozások és a kapcsolat kódot. Emellett igénybe veheti a beépített támogatást nyújt a különböző népszerű identitáskezelési rendszerek, például az Azure AD-hitelesítés a Facebook, Twitter és a Microsoft Accounts.

## <a name="supported-project-types"></a>Támogatott projekttípusok
> [!NOTE]
> A Visual Studio 2015 Azure Mobile Services hozzáadása egy univerzális Windows (Windows 10) projektek a csatlakoztatott szolgáltatás hozzáadása párbeszédpanelen nem támogatott. Azure Mobile Services adhat hozzá a NuGet Package Manager használatával a projekt számára a megfelelő csomagok telepítése.
> 
> 

A csatlakoztatott szolgáltatás párbeszédpanel segítségével csatlakozhat az Azure Mobile Services a következő típusú projekt.

* .NET Windows 8.1 Store, Phone és univerzális alkalmazás projektek
* JavaScript Windows 8.1 Store, Phone és univerzális alkalmazás projektek
* A Visual Studio Tools használata az Apache Cordova-projektet

## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Csatlakozhat az Azure Mobile Services segítségével a csatlakoztatott szolgáltatás hozzáadása párbeszédpanel
1. Győződjön meg arról, hogy az Azure-fiók. Ha nincs Azure-fiókja, regisztráljon egy [ingyenes próbaverzióra](http://go.microsoft.com/fwlink/?LinkId=518146).
2. Nyissa meg a **csatlakoztatott szolgáltatás hozzáadása** párbeszédpanel bezárásához.
   
   * A .NET-alkalmazások esetén nyissa meg a projektjét a Visual Studióban, a megnyíló helyi menüből a a **hivatkozások** csomópont a Megoldáskezelőben, majd **csatlakoztatott szolgáltatás hozzáadása**
     
        ![Az Azure Mobile szolgáltatáshoz való csatlakozás](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)
   * Az Apache Cordova-alkalmazás projektek esetében nyissa meg a projektjét a Visual Studióban, a Megoldáskezelőben nyissa meg a projekt csomópontjának helyi menüjére és válassza a **csatlakoztatott szolgáltatás hozzáadása**.
3. Az a **csatlakoztatott szolgáltatás hozzáadása** párbeszédpanelen válassza ki **Azure Mobile Services**, majd válassza a **konfigurálása** gombra. A rendszer felkérheti az Azure-bA jelentkezik, ha ezt még nem tette meg.
   
    ![Egy Azure-mobilszolgáltatás hozzáadása](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)
4. Az a **Azure Mobile Services** párbeszédpanelen válasszon ki egy meglévő mobilszolgáltatás, ha rendelkezik ilyennel. Ha szeretne egy új Azure mobilszolgáltatások létrehozását, ehhez az alábbi eljárást követve. Egyéb esetben folytassa a következő lépéssel.
   
    Egy új mobilszolgáltatás-fiók létrehozása:
   
   1. Válassza a ** szolgáltatás-Létrehozás ** hivatkozásra a párbeszédpanel alján.
       ![Új mobil csatlakoztatott szolgáltatás hozzáadása](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)
   2. Az a **mobilszolgáltatás létrehozása** párbeszédpanelen kiválaszthatja egy JavaScript mobil háttérszolgáltatást, vagy egy .NET háttérkomponenssel rendelkező mobilszolgáltatásokban a a **futásidejű** legördülő listából. 
      
       ![Mobilszolgáltatás létrehozása](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)
      
       A JavaScript háttérrendszer "szolgáltatás", egyszerű és hatékony. JavaScript háttérrendszer mobilszolgáltatás létrehozása, ha a kiszolgálóoldali JavaScript-kódot a felhőben tárolt, de szerkesztheti a kiszolgálóoldali parancsprogramok a Server Explorerben vagy az Azure felügyeleti portálján. 
      
       .NET-háttérrendszer mobil szolgáltatás lehetővé teszi a teljes erejét és rugalmasságát nyújtja a webes API-t és az Entity Framework. Ha létrehoz egy .NET mobil háttérszolgáltatást, egy projektet az Ön számára létrehozott és a megoldáshoz hozzáadott. 
   3. Válassza ki a **régió** hol kívánja elvégezni a mobilszolgáltatás, majd adja meg egy felhasználónevet és jelszót a kiszolgáló.
   4. Miután megadta a szükséges adatokat, válassza ki a **létrehozás** gombra a mobilszolgáltatás létrehozásához.
   5. Az új mobilszolgáltatás kell megjelennie a szolgáltatások listájában a **Azure Mobile Services** párbeszédpanel bezárásához. Az új mobilszolgáltatás válasszon a listából, és válassza a **Hozzáadás** gombra kattintva adja hozzá a szolgáltatás a projekthez.
5. Tekintse át az első lépésekhez oldal jelenik meg, és ismerje meg a projekt módosításának módját. Első lépések lap megjelenik, amikor a csatlakoztatott szolgáltatás hozzáadása a böngészőben. Tekintse át a javasolt következő lépések és példák, vagy váltson át a Mi történt oldalt, hogy milyen hivatkozások lettek hozzáadva a projekthez, és hogyan sikeresen módosítva. a kód és a konfigurációs fájlok.
6. A Kódminták segítségképp indítsa el a kódot, amely a mobilszolgáltatás írása!

## <a name="how-your-project-is-modified"></a>Hogyan módosul a projekthez
Hogyan módosítja a Visual Studio a projektjéhez attól függ, hogy a projekt típusa. C# ügyfélalkalmazások, lásd: [Mi történt – C# projekty](http://go.microsoft.com/fwlink/p/?LinkId=513119). JavaScript-ügyfélalkalmazások, lásd: [Mi történt – JavaScript-projektek](http://go.microsoft.com/fwlink/p/?LinkId=513120). A Cordova-alkalmazások esetén lásd: [Mi történt – a Cordova-projekt](http://go.microsoft.com/fwlink/p/?LinkId=513116).

## <a name="next-steps"></a>További lépések
Tegyen fel kérdéseket, és kérjen segítséget: 

* [MSDN-fórum: Az Azure Mobile Services](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)
* [Az Azure Mobile Services a Microsoft Azure-csapat blogja](https://azure.microsoft.com/blog/topics/mobile/)
* [Az Azure Mobile Services azure.microsoft.com:](https://azure.microsoft.com/services/mobile-services/)
* [Az Azure.microsoft.com portált, az Azure Mobile Services dokumentációja](https://azure.microsoft.com/documentation/services/mobile-services/)

