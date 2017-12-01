---
title: "Egyéni tartomány hozzáadása a CDN-végpontot |} Microsoft Docs"
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
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: fd36b94c64ad31064dbb2e0badceaee5e5bc400f
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>Egyéni tartomány hozzáadása a CDN-végpontot
Profil létrehozása után általában is létrehozhat egy vagy több CDN [végpontok](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint) (altartománya `azureedge.net`) a tartalom HTTP és HTTPS használatával. Alapértelmezés szerint a végpont összes az URL-cím szerepel (például `https://contoso.azureedge.net/photo.png`). Az Ön kényelme érdekében Azure CDN teszi lehetővé, hogy egyéni tartományt (például `www.contoso.com`) a végponttal. Ezzel a beállítással egyéni tartományt a tartalmat továbbít a végpont helyett használ. Ez a beállítás akkor hasznos, ha például azt szeretné, saját tartománynevét tartozó céljából kell láthatók az ügyfelek számára.

Ha még nem rendelkezik egyéni tartományt, először szerezzen be egy tartományi szolgáltatóhoz. Ha beszerezte a egyéni tartományt, kövesse az alábbi lépéseket:
1. [A DNS-rekordokat, a tartomány szolgáltató eléréséhez](#step-1-access-dns-records-by-using-your-domain-provider)
2. [A CNAME DNS rekord létrehozása](#step-2-create-the-cname-dns-records)
    - 1. lehetőség: Saját egyéni tartománynévre történő a CDN-végpont közvetlen leképezése
    - 2. lehetőség: A CDN-végponthoz cdnverify használatával az egyéni tartomány leképezése 
3. [Az Azure-ban a CNAME rekord hozzárendelésének engedélyezése](#step-3-enable-the-cname-record-mapping-in-azure)
4. [Győződjön meg arról, hogy az egyéni altartomány hivatkozik a CDN-végpontot](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint)
5. [(Függő lépés) Az állandó egyéni tartomány leképezése a CDN-végpont](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint)

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>1. lépés: A DNS hozzáférési rögzíti a tartomány-szolgáltató használatával

Ha Azure segítségével végzi a gazdagép a [DNS-tartományok](https://docs.microsoft.com/en-us/azure/dns/dns-overview), a tartomány-szolgáltató DNS az Azure DNS-kell delegálni. További információkért lásd: [az Azure DNS-tartomány delegálása](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)

Ellenkező esetben a tartomány szolgáltató használatakor a DNS-tartomány kezeléséhez jelentkezzen be a webhely a tartomány szolgáltató. A lapon található a következő DNS-rekordok kezelése által egyeztetett a szolgáltató dokumentációját, illetve a területek a webhely feliratú **tartománynév**, **DNS**, vagy **neve kezelő**. Gyakran, megtalálhatja a DNS-rekordok lapon megtekintésével a fiók adatait, és keres egy hivatkozást, mint **a tartományok**. Egyes szolgáltatók különböző típusú rekordok hozzáadásához különböző kapcsolat van.

> [!NOTE]
> Bizonyos szolgáltatók (például a GoDaddy) esetében a DNS-rekordok módosításai csak a külön **Módosítások mentése** hivatkozás kiválasztása után lépnek életbe. 


## <a name="step-2-create-the-cname-dns-records"></a>2. lépés: A CNAME DNS rekord létrehozása

Használatához az egyéni tartománynév az Azure CDN-végponthoz, akkor először létre kell hoznia egy kanonikus név (CNAME) rekord a tartomány szolgáltatónál. Egy olyan CNAME rekordot is egy rekord a a tartomány nevét (DNS), amely leképezhető a forrástartomány cél tartomány alias tartomány nevét a "kanonikus" vagy igaz tartománynév megadásával. Az Azure CDN a forrástartomány az egyéni tartomány (és altartomány) és a cél tartományban a CDN-végpontot. Az Azure CDN ellenőrzi a a CNAME DNS-rekord hozzáadásakor az egyéni tartomány a végpontra a portál vagy a API. 

Egy olyan CNAME rekordot leképezi a megadott tartomány és altartomány, például a `www.contoso.com` vagy `cdn.contoso.com`; nincs lehetőség egy olyan CNAME rekordot hozzárendelése egy gyökértartomány, például a `contoso.com`. Altartomány társítható csak egy CDN-végpontot, és az Ön által létrehozott CNAME rekord átirányítja az összes forgalom a megadott végpontot a altartomány fogadását. Például, ha társította `www.contoso.com` a CDN-végponthoz, akkor nem társíthatja egy másik Azure-végpont például a tárolási fiók végpontját vagy egy felhőalapú szolgáltatás végpontjának. Azonban használható különböző altartományok ugyanabban a tartományban lévő különböző végpontokhoz. Különböző altartományok azonos a CDN-végpont is leképezheti.

Az egyéni tartomány leképezése a CDN-végpontok használja az alábbi lehetőségek közül:

- 1. lehetőség: Közvetlen leképezés. Ha az egyéni tartomány semmilyen éles forgalom fut, leképezheti egyéni tartományt a CDN-végpontok közvetlenül. Az egyéni tartomány hozzárendelését a CDN-végpont lehet, hogy rövid idő alatt a tartomány állásidőt eredményezhettek, miközben a regisztrál a tartományt az Azure portálon. A CNAME-leképezés bejegyzés a következő formátumban kell lennie: 
 
  | NÉV             | TÍPUS  | ÉRTÉK                  |
  |------------------|-------|------------------------|
  | www\.contoso.com | CNAME | Contoso\.azureedge.net |


- 2. lehetőség: Hozzárendelés a **cdnverify** altartomány. Éles forgalmat nem lehet megszakítani az egyéni tartomány fut, ha a CDN-végpontot létrehozhat egy ideiglenes CNAME-leképezés. Ezzel a beállítással használhatja az Azure **cdnverify** altartomány egy köztes regisztrációs lépésében biztosításához, hogy a felhasználók férhetnek hozzá a tartomány a DNS-hozzárendelése során megszakítás nélkül történik.

   1. Új CNAME rekordot kell létrehozni, és adjon meg egy altartomány alias, amely tartalmazza a **cdnverify** altartomány. Például **cdnverify.www** vagy **cdnverify.cdn**. 
   2. Adja meg a gazdagép neve, amely a CDN-végpontot, a következő formátumban: `cdnverify.<EndpointName>.azureedge.net`. A CNAME-leképezés bejegyzés a következő formátumban kell lennie: 

   | NÉV                       | TÍPUS  | ÉRTÉK                            |
   |----------------------------|-------|----------------------------------|
   | cdnverify.www\.contoso.com | CNAME | cdnverify.contoso\.azureedge.net | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>3. lépés: Engedélyezze a CNAME rekord hozzárendelése az Azure-ban

Az egyéni tartomány a fenti eljárások egyikével regisztrálása után az egyéni tartomány adott szolgáltatást az Azure CDN majd engedélyezheti. 

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) , és keresse meg a CDN-profil hozzárendelése egyéni tartományhoz kívánja a végponthoz.  
2. Az a **CDN-profil** panelen válassza ki, amelyhez az altartomány társítani szeretné a CDN-végpont.
3. A végpont panel bal felső sarokban kattintson **egyéni tartomány**. 

   ![Az egyéni tartomány gomb](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. Az a **egyéni állomásnév** szöveg mezőbe írja be az egyéni tartomány, beleértve a altartományt terheli. Például `www.contoso.com` vagy `cdn.contoso.com`.

   ![Adja hozzá az egyéni tartomány párbeszédpanel](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. Kattintson az **Add** (Hozzáadás) parancsra.

   Az Azure ellenőrzi, hogy a megadott tartománynév esetében létezik-e a CNAME-rekord. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz. A CNAME rekord névkiszolgálóira propagálása némi időbe telhet. Ha a tartomány nincs érvényesítve azonnal, ellenőrizze, hogy helyes-e a CNAME rekordot, majd várjon néhány percet, és próbálkozzon újra. A **Azure CDN Verizon** (Standard és prémium) végpont, is igénybe vehet az egyéni tartomány beállítások az összes CDN peremhálózati csomóponthoz propagálása 90 percig.  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>4. lépés: Győződjön meg arról, hogy az egyéni altartomány hivatkozik a CDN-végpontot

Az egyéni tartomány a regisztráció befejezését követően győződjön meg arról, hogy az egyéni altartomány hivatkozik a CDN-végpontot.
 
1. Győződjön meg arról, hogy rendelkezik-e, hogy a rendszer gyorsítótárba helyezte a végpontot a nyilvános tartalmakhoz. Például ha a CDN-végpontot a storage-fiók tartozik, a gyorsítótárazza a CDN a nyilvános blob-tárolókban lévő tartalmat. Az egyéni tartomány teszteléséhez győződjön meg arról, hogy a tároló van beállítva, hogy a nyilvános hozzáférést, és tartalmaz legalább egy blob.

2. A böngészőben nyissa meg a blob-címére a saját tartománynév használatával. Például, ha az egyéni tartomány `cdn.contoso.com`, a gyorsítótárban lévő blob URL-CÍMÉT a következő URL-cím hasonló lesz: `http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`.


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>(Függő lépés) 5. lépés: a végleges egyéni tartomány leképezése a CDN-végpont

Ez a lépés nem függ a 2. lépés, 2. lehetőség (hozzárendelés a **cdnverify** altartomány). Ha használja az ideiglenes **cdnverify** altartomány és ellenőrizte, hogy működik, majd leképezheti az állandó egyéni tartomány a CDN-végpont.

1. A tartomány gyártójának webhelyén az állandó egyéni tartomány leképezése a CDN-végpont CNAME DNS rekordot kell létrehozni. A CNAME-leképezés bejegyzés a következő formátumban kell lennie: 
 
   | NÉV             | TÍPUS  | ÉRTÉK                  |
   |------------------|-------|------------------------|
   | www\.contoso.com | CNAME | Contoso\.azureedge.net |
2. A CNAME rekord, törölje a **cdnverify** altartományt, amelyet korábban hozott létre.

## <a name="see-also"></a>Lásd még:
[A Content Delivery Network (CDN) engedélyezése az Azure-bA](cdn-create-new-endpoint.md)  
[Az Azure DNS-tartomány delegálása](../dns/dns-domain-delegation.md)
