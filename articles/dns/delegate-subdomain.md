---
title: Az Azure DNS-altartomány delegálása
description: Learn how to delegate an Azure DNS subdomain.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: victorh
ms.openlocfilehash: 31543db8e177701ddfe6beaaa3091d6465b0e9cd
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895480"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Az Azure DNS-altartomány delegálása

Az Azure portal segítségével egy DNS-altartomány delegálása. Például ha Ön a tulajdonosa a contoso.com tartományhoz, akkor is engedélyezheti – delegálja nevű altartomány *mérnöki* , egy másik zónához, amely a contoso.com zóna a külön-külön felügyelheti.

Igény szerint egy altartományt az delegálhatja [Azure PowerShell-lel](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure DNS altartományában delegálni, először delegálnia kell az Azure DNS nyilvános tartományát. Lásd: [tartomány delegálása az Azure DNS](./dns-delegate-domain-azure-dns.md) vonatkozó utasításokat a névkiszolgálókat a delegálás konfigurálása. Az Azure DNS-zónabeli tartományához delegált, miután az altartomány delegálhat.

> [!NOTE]
> A contoso.com egész cikkben példaként szolgál. A contoso.com helyére írja be a saját tartománynevét.

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

Next, note the four name servers for the engineering subdomain.

Az a **mérnöki** zóna panelen jegyezze fel a négy névkiszolgálói nevet a zóna. Ezektől a névkiszolgálóktól később fogja használni.

## <a name="create-a-test-record"></a>Test-rekord létrehozása

Hozzon létre egy **A** teszteléshez használni kívánt rekord. Hozzon létre például egy **www** A jegyezze fel, és állítsa be úgy a egy **10.10.10.10** IP-címet.

## <a name="create-an-ns-record"></a>NS-rekord létrehozása

Ezután hozzon létre egy név névkiszolgáló (NS) rekordot a **mérnöki** zóna.

1. Keresse meg a szülő tartomány zónáját.
2. Kattintson a **+ Rekordhalmaz** gombra.
3. Az a **rekordhalmaz hozzáadása** ablak, írja be **mérnöki** a a **neve** szövegmezőben.
4. A **típus**válassza **NS**.
5. A **névkiszolgáló**, írja be a négy névkiszolgálói nevet, amely a korábban rögzített a **mérnöki** zóna.
6. Kattintson az **OK** gombra.

## <a name="test-the-delegation"></a>A delegálás tesztelése

Az nslookup követve tesztelheti a delegálást.

1. Nyisson meg egy PowerShell-ablakot.
2. Írja be a parancssorba `nslookup www.engineering.contoso.com.`
3. Ekkor kap egy nem mérvadó válasz, a cím megjelenítése **10.10.10.10**.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [fordított DNS az Azure-ban üzemeltetett szolgáltatások konfigurálása](dns-reverse-dns-for-azure-services.md).