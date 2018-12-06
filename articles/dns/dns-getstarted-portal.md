---
title: Rövid útmutató – DNS-zóna létrehozása, és jegyezze fel az Azure portal használatával
description: A részletes rövid útmutató segítségével megtudhatja, hogyan hozhat létre az Azure DNS-zóna, és jegyezze fel az Azure portal használatával.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: 9929662f1fe4612e51c82248f64e3191f7fdb223
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52955205"
---
# <a name="quickstart-configure-azure-dns-for-name-resolution-by-using-the-portal"></a>Gyors útmutató: Azure DNS konfigurálása a névfeloldáshoz a portál használatával

Az Azure DNS-t konfigurálhatja úgy, hogy feloldja a gazdagépneveket a nyilvános tartományban. Például, ha vásárolt a *contoso.com* tartománynév a tartományregisztrálóhoz, konfigurálhatja az Azure DNS-gazdagépre a *contoso.com* tartományi és feloldása *www.contoso.com* IP-címet a webalkalmazás-kiszolgáló vagy a webalkalmazás.

Ebben a rövid útmutatóban létrehoz egy tesztelési tartományhoz, és majd hozzon létre egy cím rekord feloldása *www* IP-címet *10.10.10.10*.

>[!IMPORTANT]
>A neveket és IP-címeket ebben a rövid útmutatóban olyan, amelyek nem felelnek meg a valós életből vett példák. Ez a rövid útmutató ismerteti valós következmények is, ha vannak ilyenek.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Az összes lépései a Portalon, jelentkezzen be a [az Azure portal](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>DNS-zóna létrehozása

DNS-zóna egy tartomány DNS-bejegyzéseket tartalmaz. Üzemeltessen saját tartományt az Azure DNS-ben, hozzon létre egy DNS-zónát az adott tartománynévhez. 

**A DNS-zóna létrehozása:**

1. A bal felső sarokban, válassza **erőforrás létrehozása**, majd **hálózatkezelés**, majd **DNS-zóna**.
   
1. Az a **DNS-zóna létrehozása** lapon adja meg a következő értékeket:
   
   - **Név**: típus *contoso.xyz* ebben a rövid példában. A DNS-zóna nevét, amely még nem konfigurálták az Azure DNS-kiszolgálókon bármilyen érték lehet. Valós érték lehet például egy tartomány, amelyet egy tartományregisztrálótól vásárolt.
   - **Erőforráscsoport**: válasszon **új létrehozása**, adja meg *dns-teszt*, és válassza ki **OK**. Az erőforráscsoport nevét az Azure-előfizetésen belül egyedinek kell lennie. 
   
1. Kattintson a **Létrehozás** gombra.

   ![DNS-zóna](./media/dns-getstarted-portal/openzone650.png)
   
A zóna létrehozása eltarthat néhány percig.

## <a name="create-a-dns-record"></a>DNS-rekord létrehozása

A tartomány a DNS-zóna belül hoz létre DNS-bejegyzések vagy rekordok. Hozzon létre egy új cím rekord vagy oldható fel IPv4-cím állomásnév "A" rekord.

**Az "A" rekord létrehozása:**

1. Az Azure Portalon alatt **összes erőforrás**, nyissa meg a **contoso.xyz** DNS-zónára a **dns-teszt** erőforráscsoportot. Megadhat *contoso.xyz* a a **Szűrés név alapján** jelölőnégyzetet, hogy könnyebben megtalálják.

1. Felső részén a **DNS-zóna** lapon jelölje be **+ rekordhalmaz**.

1. Az a **rekordhalmaz hozzáadása** lapon adja meg a következő értékeket:

   - **Név**: típus *www*. A rekord név a gazdagép nevét, amelyet szeretne hozzárendelni a megadott IP-cím.
   - **Típus**: válasszon **A**. "A" rekordok a leggyakrabban előforduló, de más rekord típusa levelezőkiszolgálók ("MX"), IPv6-címeket (AAAA) és így tovább. 
   - **Élettartam**: típus *1*. *Time-to-live* a DNS-kérelem Megadja, mennyi ideig DNS-kiszolgálók és ügyfelek is gyorsítótárazza a választ.
   - **Élettartam mértékegysége**: válasszon **óra**. Ez az a időegysége a **TTL** értéket. 
   - **IP-cím**: írja be a jelen rövid példában *10.10.10.10*. Ez az érték, oldja fel az IP-címet a rekord nevébe. A való életből vett helyzet kell beírnia a nyilvános IP-címet a webkiszolgáló.

Mivel ez a rövid útmutató egy valódi tartomány nem használ, nincs nem kell konfigurálni az Azure DNS névkiszolgálókat a tartománynév regisztrálójánál az. Egy valódi tartomány érdemes bárki hozzáférhet az interneten keresztül csatlakozni a webalkalmazás-kiszolgáló vagy alkalmazás az állomásnév feloldása. A tartománynév regisztrálójához, és cserélje le az Azure DNS névkiszolgálóit névkiszolgálói rekordjainak fog keresse fel. További információkért lásd: [oktatóanyag: üzemeltessen saját tartományt az Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>A névfeloldás tesztelése

Most, hogy a teszt DNS-zóna egy teszt "A" rekord, ezt kipróbálhatja a névfeloldás nevű eszközt *nslookup*. 

**DNS-névfeloldás tesztelése:**

1. Az Azure Portalon alatt **összes erőforrás**, nyissa meg a **contoso.xyz** DNS-zónára a **dns-teszt** erőforráscsoportot. Megadhat *contoso.xyz* a a **Szűrés név alapján** jelölőnégyzetet, hogy könnyebben megtalálják.

1. Másolja ki az egyik a névkiszolgáló nevek kiszolgálóját a kiszolgálólistából neve az a **áttekintése** lapot. 
   
   ![zóna](./media/dns-getstarted-portal/viewzonens500.png)
   
   >[!NOTE]
   >A való életből vett helyzet összes négy névkiszolgálói nevet, beleértve a záró pontot, másolja, és alkalmazza őket az új Azure DNS névkiszolgálói nevet, a tartományregisztrálónál. További információkért lásd: [tartomány delegálása az Azure DNS-ben](dns-delegate-domain-azure-dns.md)
   
1. Nyisson meg egy parancssort, és futtassa a következő parancsot:

   ```
   nslookup <host name> <name server name>
   ```
   
   Példa:
   
   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```
   
   Hiba a következő képernyőhöz hasonlóan kell megjelennie:
   
   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Az állomásnév **www.contoso.xyz** mutat **10.10.10.10**, ugyanúgy, mint a be vannak állítva ezek. Ezt az eredményt ellenőrzi, hogy a névfeloldás megfelelően működik-e. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a rövid útmutatóban létrehozott erőforrásokat, távolítsa el őket törlésével a **dns-teszt** erőforráscsoportot. Nyissa meg a **dns-teszt** erőforrás-csoportba, és válassza ki **erőforráscsoport törlése**.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)