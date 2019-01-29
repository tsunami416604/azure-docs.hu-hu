---
title: Az Azure AD Connect - frissítés az AD FS-farm SSL-tanúsítvány |} A Microsoft Docs
description: Ez a dokumentum ismerteti a lépéseket egy AD FS-farm SSL-tanúsítvány frissítése az Azure AD Connect használatával.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory  
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.openlocfilehash: 0126b14aab5ffc861ede624878c619e246b6d2b3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55167121"
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Az Active Directory összevonási szolgáltatások (AD FS) farm SSL-tanúsítvány frissítése

## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan használhatja az Azure AD Connect egy Active Directory összevonási szolgáltatások (AD FS) farm SSL-tanúsítvány frissítéséhez. Az Azure AD Connect eszköz használatával egyszerűen frissítheti az AD FS-farm SSL-tanúsítványát, még akkor is, ha a felhasználó bejelentkezési kiválasztott metódus nem AD FS.

A teljes művelet frissítési SSL-tanúsítványt az AD FS-farm minden összevonási és webalkalmazás-proxykiszolgálóként (WAP) kiszolgálók három egyszerű lépésben végezheti el:

![Három lépésben](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Az AD FS által használt tanúsítványok kapcsolatos további információkért lásd: [AD FS által használt tanúsítványok ismertetése](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Előfeltételek

* **AD FS-Farm**: Győződjön meg arról, hogy az AD FS farm Windows Server 2012 R2-alapú vagy újabb.
* **Azure AD Connect**: Győződjön meg arról, hogy az Azure AD Connect verziója 1.1.553.0 vagy újabb verziója. A feladat használni kívánt **frissítés AD FS SSL-tanúsítvány**.

![SSL-feladat frissítése](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>1. lépés: Az AD FS-farm információinak megadása

Az Azure AD Connect megkísérli automatikusan az AD FS-farm adatainak lekéréséhez:
1. (Windows Server 2016 vagy újabb) az AD FS farm adatainak lekérdezésekor.
2. Hivatkozik az adatokat a korábbi közvetítésekből származó, amelyet a rendszer helyben tárolja az Azure AD Connecttel.

Módosíthatja a kiszolgálók hozzáadásával vagy eltávolításával a kiszolgálók az AD FS-farm aktuális konfigurációjának megfelelően megjelenő listáját. Amint a kiszolgáló információ áll rendelkezésre, az Azure AD Connect a kapcsolat és SSL-tanúsítvány aktuális állapotát jeleníti meg.

![AD FS-kiszolgáló adatai](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Ha a lista tartalmazza a kiszolgáló, amely már nem az AD FS-farm része, kattintson a **eltávolítása** törölni a kiszolgálót az AD FS farmon, a kiszolgálók listájában.

![A listában a kapcsolat nélküli kiszolgáló](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Kiszolgáló eltávolítása a listából egy AD FS-farm, az Azure AD Connect-kiszolgálók helyi művelet, és frissíti az AD FS-farm, amely az Azure AD Connect helyben tárolja az adatait. Az Azure AD Connect nem módosítja a konfigurációt, az AD FS-ben a változás tükrözése érdekében.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>2. lépés: Adjon meg egy új SSL-tanúsítvány

Miután jóváhagyta az információ a farm AD FS-kiszolgálók, az Azure AD Connect új SSL-tanúsítványt kér. Adja meg a jelszóval védett PFX-tanúsítvány a telepítés folytatásához.

![SSL-tanúsítvány](./media/how-to-connect-fed-ssl-update/certificate.png)

Miután megadta a tanúsítványt, az Azure AD Connect végighalad egy sorozatát előfeltételek. Győződjön meg arról, hogy a tanúsítvány megfelelő-e az AD FS-farm számára a tanúsítványt:

-   A tanúsítvány tulajdonos neve vagy a másodlagos tulajdonosnév vagy azonos az összevonási szolgáltatás neve, vagy helyettesítő tanúsítvány.
-   A tanúsítvány a több mint 30 napig érvényes.
-   Érvénytelen a tanúsítvány megbízhatósági láncában.
-   A tanúsítvány jelszóval védve.

## <a name="step-3-select-servers-for-the-update"></a>3. lépés: A frissítés-kiszolgálók kiválasztása

A következő lépésben válassza ki a kiszolgálókat, az SSL-tanúsítvány frissítése rendelkeznie kell. Az offline kiszolgálók nem választható ki a frissítést.

![Válassza ki a kiszolgálók frissítése](./media/how-to-connect-fed-ssl-update/selectservers.png)

Miután elvégezte a konfigurációt, az Azure AD Connect az üzenetet, amely azt jelzi, hogy a frissítés állapotát, és ellenőrizheti az AD FS bejelentkezési lehetőséget biztosít jeleníti meg.

![A konfigurálás befejeződött](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Gyakori kérdések

* **Mit kell lennie a tanúsítvány tulajdonosának nevét az új AD FS SSL-tanúsítvány számára?**

    Az Azure AD Connect ellenőrzi, hogy ha a tanúsítvány tulajdonos neve vagy másodlagos tulajdonosának neve tartalmazza az összevonási szolgáltatás neve. Például ha az összevonási szolgáltatás nevének pedig az fs.contoso.com, a tulajdonos neve vagy a másodlagos tulajdonosnév kell lennie: fs.contoso.com.  Helyettesítő karaktert tartalmazó tanúsítványok is használhatók.

* **Miért kérdezi meg az alkalmazás a hitelesítő adatokat újra a WAP-kiszolgáló oldalán?**

    Ha a hitelesítő adatokat ad meg az AD FS-kiszolgálók összekapcsolása a még nem rendelkezik a jogosultság a WAP-kiszolgálók kezelése, az Azure AD Connect az a WAP-kiszolgálókon rendszergazdai jogosultságokkal rendelkező hitelesítő adatokat kér.

* **A kiszolgáló offline állapotúként jelenik meg. Mit tegyek?**

    Az Azure AD Connect bármely operaci nelze provést, ha a kiszolgáló nem érhető el. Ha a kiszolgáló az AD FS-farm része, majd ellenőrizze a kapcsolatot a kiszolgálóval. Miután megoldotta a problémát, nyomja le az frissítheti az állapotot, a varázsló a frissítés ikont. Ha a kiszolgáló nem része a farm korábban, de most már nem létezik, kattintson a **eltávolítása** való törléséhez a kiszolgálók listáját, hogy az Azure AD Connect kezeli. Kiszolgáló eltávolítása a listából az Azure AD Connect nem módosítható az AD FS konfigurációt magát. Ha az AD FS a Windows Server 2016 vagy újabb, illetve a kiszolgáló marad, a konfigurációs beállításokat használja, és a következő alkalommal újra megjelenik a feladat futtatása.

* **Frissíthetem a webfarm-kiszolgálók egy része az új SSL-tanúsítvány?**

    Igen. A feladat mindig futtatható **SSL-tanúsítvány frissítése** újra a frissítés a többi kiszolgálón. Az a **kiszolgálók kijelölése az SSL-tanúsítvány frissítéséhez** lapon rendezheti a kiszolgálók listáját **SSL lejárati dátuma** is egyszerűen hozzáférhet a kiszolgálókat, amelyeket nem frissítenek még.

* **Az előző futtatásból távolítva az a kiszolgáló, de továbbra is alatt látható, kapcsolat nélküli és a listán szereplő az AD FS-kiszolgálók lapon. Miért van a kapcsolat nélküli kiszolgáló továbbra is van, még akkor is, miután eltávolított?**

    Kiszolgáló eltávolítása a listából az Azure AD Connect nem távolítható el a az AD FS konfigurációt. Az Azure AD Connect a farm minden információt az AD FS (Windows Server 2016-os vagy magasabb) hivatkozik. Ha a kiszolgáló továbbra is megtalálhatók az AD FS konfigurációt, azt vissza a listában megjelennek.  

## <a name="next-steps"></a>További lépések

- [Azure AD Connect és összevonás](how-to-connect-fed-whatis.md)
- [Az Active Directory összevonási szolgáltatások kezelése és testreszabása az Azure AD Connecttel](how-to-connect-fed-management.md)
