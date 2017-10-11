---
title: "Azure CDN-tartalom hozzárendelése egyéni tartományhoz |} Microsoft Docs"
description: "Útmutató: Azure CDN-tartalom hozzárendelése egyéni tartományhoz."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: cd6db44f7776859d1e6a893543cf0666182ca41a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="map-azure-cdn-content-to-a-custom-domain"></a>Azure CDN-tartalom hozzárendelése egyéni tartományhoz
Ahhoz, hogy az URL-címek saját tartománynév használatával azureedge.net altartománya használata helyett a gyorsítótárazott tartalmat a CDN-végpont egyéni tartományt is leképezheti.

Az egyéni tartomány leképezése a CDN-végpont két módja van:

1. [Hozzon létre egy CNAME rekordot a tartományregisztrálónál és az egyéni tartomány és altartomány van leképezve a CDN-végpont](#register-a-custom-domain-for-an-azure-cdn-endpoint).
   
    A CNAME-rekord egy DNS-szolgáltatás, amellyel egy forrástartomány (pl.: `www.contosocdn.com` vagy `cdn.contoso.com`) leképezhető egy céltartományra. Ebben az esetben a forrástartomány az egyéni tartomány és altartomány (altartomány, például **www** vagy **cdn** megadása mindig kötelező). A cél tartományban a CDN-végpontot.  
   
    Az egyéni tartomány hozzárendelését a CDN-végpont, azonban eredményezhet a tartomány állásidő rövid idő alatt közben regisztrál a tartományt az Azure portálon.
2. [Adjon hozzá egy köztes regisztrációs lépést a **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)
   
    Ha az egyéni tartomány jelenleg támogat az alkalmazás a szolgáltatásiszint-szerződés (SLA), amely megköveteli, hogy nincs állásidő nélkül, akkor is használhatja az Azure **cdnverify** altartomány egy köztes regisztrációs lépésében adja meg így a felhasználók tudnak a tartomány eléréséhez, amíg a DNS-hozzárendelése kerül sor.  

Az egyéni tartomány, a fenti eljárások egyikével regisztrálása után érdemes [győződjön meg arról, hogy az egyéni altartomány hivatkozik-e a CDN-végpont](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [!NOTE]
> Egy olyan CNAME rekordot a tartományregisztrálónál a tartomány hozzárendelését a CDN-végpont kell létrehoznia. A CNAME-rekordok leképezése adott altartományok például `www.contoso.com` vagy `cdn.contoso.com`. Nincs lehetőség egy olyan CNAME rekordot hozzárendelése egy gyökértartomány, például a `contoso.com`.
> 
> Altartomány csak társítható egy CDN-végpontot. A CNAME rekord, az Ön által létrehozott összes forgalom fogadását a megadott végpontot a altartomány irányítja.  Például, ha társította `www.contoso.com` a CDN-végponthoz, akkor nem kapcsolhat hozzá más Azure-végpontok, például a tárolási fiók végpontját vagy egy felhőalapú szolgáltatás végpontjának. Azonban használható különböző altartományok ugyanabban a tartományban lévő különböző végpontokhoz. Különböző altartományok azonos a CDN-végpont is leképezheti.
> 
> A **Azure CDN Verizon** (Standard és prémium) végpont, vegye figyelembe, hogy tart legfeljebb **90 percig** CDN propagálása egyéni tagságának módosítása a szegély csomópontok.
> 
> 

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Egy egyéni tartományt az Azure CDN-végpont regisztrálása
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/).
2. Kattintson a **Tallózás**, majd **CDN-profilra**, akkor a CDN-profilt a végpont kívánt hozzárendelése egyéni tartományhoz.  
3. Az a **CDN-profil** panelen kattintson a CDN-végpontot, amelyhez az altartomány társítani szeretné.
4. A végpont panel felső részén kattintson a **egyéni tartomány hozzáadása** gombra.  Az a **hozzá egyéni tartományt** panelen láthatja végpont állomásneve, a CDN-végpontot, új CNAME rekord létrehozásával használandó származik. A gazdagép címe formátumban jelenik meg  **&lt;EndpointName >. azureedge.net**.  A használatára a CNAME rekord létrehozásával állomásnevet másolhatja.  
5. Lépjen a tartományregisztráló webhelyére, és keresse meg a DNS-rekordok létrehozására szolgáló felületet. Ezt a **Tartománynév**, **DNS**, **Névkiszolgáló kezelése** vagy hasonló területen találja.
6. Keresse meg a CNAME-rekordok kezelésére szolgáló felületet. Ehhez esetleg a különleges beállítások lapjára kell lépnie, és ott a CNAME, Alias vagy Altartományok kifejezést kell keresnie.
7. Hozzon létre egy új CNAME rekordot, amely leképezhető a kiválasztott altartomány (például **www** vagy **cdn**) megadott állomás nevét a **hozzá egyéni tartományt** panelen. 
8. Lépjen vissza a **hozzá egyéni tartományt** panelt, és adja meg az egyéni tartomány, a altartomány, beleértve a párbeszédpanelen. Például a formátumban adja meg a tartománynév `www.contoso.com` vagy `cdn.contoso.com`.   
   
   Azure lesz győződjön meg arról, hogy létezik-e a CNAME rekordot a megadott tartománynév. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz.  A **Azure CDN Verizon** (Standard és prémium) végpont, az egyéni tartomány beállításainak propagálása az összes CDN peremhálózati csomóponthoz, azonban legfeljebb 90 percig eltarthat.  
   
   Vegye figyelembe, hogy bizonyos esetekben időbe telhet a CNAME rekord propagálása az internetes kiszolgálókra neve. Ha a tartomány nincs érvényesítve azonnal, és feltételezi, hogy helyesek-e a CNAME rekordot, majd várjon néhány percet, és próbálkozzon újra.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Egy Azure CDN-végponthoz, a köztes cdnverify altartomány használatával az egyéni tartománynév regisztrálása
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/).
2. Kattintson a **Tallózás**, majd **CDN-profilra**, akkor a CDN-profilt a végpont kívánt hozzárendelése egyéni tartományhoz.  
3. Az a **CDN-profil** panelen kattintson a CDN-végpontot, amelyhez az altartomány társítani szeretné.
4. A végpont panel felső részén kattintson a **egyéni tartomány hozzáadása** gombra.  Az a **hozzá egyéni tartományt** panelen láthatja végpont állomásneve, a CDN-végpontot, új CNAME rekord létrehozásával használandó származik. A gazdagép címe formátumban jelenik meg  **&lt;EndpointName >. azureedge.net**.  A használatára a CNAME rekord létrehozásával állomásnevet másolhatja.
5. Lépjen a tartományregisztráló webhelyére, és keresse meg a DNS-rekordok létrehozására szolgáló felületet. Ezt a **Tartománynév**, **DNS**, **Névkiszolgáló kezelése** vagy hasonló területen találja.
6. Keresse meg a CNAME-rekordok kezelésére szolgáló felületet. Lehet, hogy egy speciális beállítások lapot, és keresse meg a szavakat **CNAME**, **Alias**, vagy **altartományok**.
7. Új CNAME rekordot kell létrehozni, és adjon meg egy altartomány alias, amely tartalmazza a **cdnverify** altartomány. Például a megadott altartomány formátumban lesz **cdnverify.www** vagy **cdnverify.cdn**. Adja meg a gazdagép neve, amely a CDN-végpontot, formátumú **cdnverify.&lt; EndpointName >. azureedge.net**. A DNS-hozzárendelése hasonlóan kell kinéznie:`cdnverify.www.consoto.com   CNAME   cdnverify.consoto.azureedge.net`  
8. Lépjen vissza a **hozzá egyéni tartományt** panelt, és adja meg az egyéni tartomány, a altartomány, beleértve a párbeszédpanelen. Például a formátumban adja meg a tartománynév `www.contoso.com` vagy `cdn.contoso.com`. Ebben a lépésben nem kell, hogy kiírja a altartomány **cdnverify**.  
   
    Azure ellenőrzi, hogy, hogy létezik-e a CNAME rekordot a megadott cdnverify tartománynév.
9. Ezen a ponton az egyéni tartomány ellenőrzése után az Azure-ban, de a forgalmat a tartományhoz nem még routedevent irányítása a CDN-végponthoz. Az egyéni tartomány beállításainak propagálása a CDN engedélyezéséhez elég hosszú várakozás után a szegély csomópontok (90 percig **Azure CDN Verizon**, 1-2 percet **Akamai Azure CDN**), lépjen vissza a DNS-kiszolgáló regisztráló webhelyén, és hozzon létre egy másik olyan CNAME rekordot, amely a altartomány van leképezve a CDN-végpontot. Adja meg az altartományt, mint például **www** vagy **cdn**, és az állomásnevet,  **&lt;EndpointName >. azureedge.net**. Az ebben a lépésben az egyéni tartomány regisztrálása sikeresen befejeződött.
10. Végül, törölheti a létrehozott CNAME rekord **cdnverify**, mint csak, egy köztes lépés szükséges.  

## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Győződjön meg arról, hogy az egyéni altartomány hivatkozik a CDN-végpontot
* Az egyéni tartomány a regisztráció befejezését követően a CDN-végpont az egyéni tartomány érheti el a gyorsítótárazott tartalmat.
  Először győződjön meg róla, hogy rendelkezik-e, hogy a rendszer gyorsítótárba helyezte a végpontot a nyilvános tartalmakhoz. Például ha a CDN-végpontot a storage-fiók tartozik, a gyorsítótárazza a CDN a nyilvános blob-tárolókban lévő tartalmat. Az egyéni tartomány teszteléséhez győződjön meg arról, hogy a tároló beállításai lehetővé teszik a nyilvános hozzáférést, és tartalmaz legalább egy blob.
* A böngészőben nyissa meg az egyéni tartomány blob-címére. Például, ha az egyéni tartomány `cdn.contoso.com`, gyorsítótárazott blob URL-CÍMÉT a következő URL-cím hasonló lesz: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Lásd még:
[A Content Delivery Network (CDN) engedélyezése az Azure-bA](cdn-create-new-endpoint.md)  

