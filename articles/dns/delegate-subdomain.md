---
title: Az Azure DNS-altartomány delegálása
description: Learn how to delegate an Azure DNS subdomain.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/18/2019
ms.author: victorh
ms.openlocfilehash: 3a7ec043f1990dd7f380177fd612c8cd88786b07
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54440226"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Az Azure DNS-altartomány delegálása

Az Azure portal segítségével egy DNS-altartomány delegálása. Például ha Ön a tulajdonosa a contoso.com tartományhoz, akkor is engedélyezheti – delegálja nevű altartomány *mérnöki* , egy másik zónához, amely képes lehet különül el a contoso.com zóna.

## <a name="prerequisites"></a>Előfeltételek

Az Azure DNS altartományában delegálni, először delegálnia kell az Azure DNS nyilvános tartományát. Lásd: [tartomány delegálása az Azure DNS](./dns-delegate-domain-azure-dns.md) vonatkozó utasításokat a névkiszolgálókat a delegálás konfigurálása. Az Azure DNS-zónabeli tartományához delegált, miután az altartomány delegálhat.

Ez a cikk a példákban a contoso.com tartományhoz. Ezek az eljárások használata esetén helyettesítse a saját tartományából.

## <a name="create-a-zone-for-your-subdomain"></a>Create a zone for your subdomain

Először hozza létre a zónát a **mérnöki** altartományt.

1. Az Azure Portalon, válassza ki a **erőforrás létrehozása**.
2. A Keresés mezőbe írja be a **DNS**, és válassza ki **DNS-zóna**.
3. Kattintson a **Létrehozás** gombra.
4. Az a **DNS-zóna létrehozása** ablak, írja be **engineering.contoso.com** a a **neve** szövegmezőben.
5. Válassza ki az erőforráscsoportot a zóna. Érdemes ugyanazt az erőforráscsoportot használja a szülőzónában hasonló erőforrások maradjon.
6. Kattintson a **Create** (Létrehozás) gombra.
7. Miután az üzembe helyezés sikeres, nyissa meg az új zóna.

## <a name="note-the-name-servers"></a>Vegye figyelembe a Névkiszolgálók

Next, copy the four name servers for your subdomain.

1. Az a **mérnöki** zóna panelen jegyezze fel a négy névkiszolgálói nevet a zóna. Ezektől a névkiszolgálóktól később fogja használni.
2. Hozzon létre egy **A** teszteléshez használni kívánt rekord. Hozzon létre például egy **www** A jegyezze fel, és állítsa be úgy a egy **10.10.10.10** IP-címet.

## <a name="create-an-ns-record"></a>NS-rekord létrehozása

Ezután hozzon létre egy név névkiszolgáló (NS) rekordot a **mérnöki** zóna.

1. Keresse meg a szülőtartomány-zónafájljában.
2. Kattintson a **+ Rekordhalmaz** gombra.
3. Az a **rekordhalmaz hozzáadása** ablak, írja be **mérnöki** a a **neve** szövegmezőben.
4. A **típus**válassza **NS**.
5. A **névkiszolgáló**, írja be a négy névkiszolgálói nevet, amely a korábban rögzített a **mérnöki** zóna.
6. Kattintson az **OK** gombra.

## <a name="test-the-delegation"></a>A delegálás tesztelése

Az nslookup követve tesztelheti a delegálást.

1. Nyisson meg egy PowerShell-ablakot.
2. Írja be a parancssorba `nslookup www.engineering.<your domain name>.`
3. Ekkor kap egy nem mérvadó válasz, a cím megjelenítése **10.10.10.10**.



## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [fordított DNS az Azure-ban üzemeltetett szolgáltatások konfigurálása](dns-reverse-dns-for-azure-services.md).