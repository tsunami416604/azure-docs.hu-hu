---
title: Altartomány delegálása – Azure DNS
description: Ezzel a tanulási útvonallal első lépések egy Azure DNS-altartomány delegálása.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: cd1443a9ca8ccf7172072078734f21d789d36194
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937440"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Azure DNS-altartomány delegálása

Az Azure Portal segítségével delegálhatja a DNS-altartomány. Ha például a contoso.com tartomány tulajdonosa, delegálhat egy *mérnöki* nevű altartományt egy másik, különálló zónába, amelyet a contoso.com zónától elkülönítve felügyelhet.

Ha szeretné, delegálhat egy altartományt az [Azure PowerShell](delegate-subdomain-ps.md)használatával.

## <a name="prerequisites"></a>Előfeltételek

Az Azure DNS-altartomány delegálásához először delegálhatja a nyilvános tartományt az Azure DNS-nek. A névkiszolgálók delegálásra való konfigurálásával kapcsolatos útmutatásért [tekintse meg a Tartomány delegálása az Azure DNS-re](./dns-delegate-domain-azure-dns.md) című témakört. Miután a tartomány delegált az Azure DNS-zónában, delegálhatja az altartományt.

> [!NOTE]
> Contoso.com példaként szolgál ebben a cikkben. A contoso.com helyére írja be a saját tartománynevét.

## <a name="create-a-zone-for-your-subdomain"></a>Zóna létrehozása az altartományhoz

Először hozza létre a **mérnöki** altartomány zónáját.

1. Az Azure Portalon válassza az **Erőforrás létrehozása lehetőséget.**
2. A keresőmezőbe írja be a **DNS**parancsot, és válassza a **DNS-zónát**.
3. Kattintson a **Létrehozás** gombra.
4. A **DNS-zóna létrehozása** ablaktáblában írja be **engineering.contoso.com** a **Név** mezőbe.
5. Válassza ki a zóna erőforráscsoportját. Előfordulhat, hogy ugyanazt az erőforráscsoportot szeretné használni, mint a szülőzóna a hasonló erőforrások együtt tartásához.
6. Kattintson **a Létrehozás gombra.**
7. Miután a központi telepítés sikeres, lépjen az új zónába.

## <a name="note-the-name-servers"></a>Jegyezze fel a névkiszolgálókat

Ezután vegye figyelembe a mérnöki altartomány négy névkiszolgálóit.

A **mérnöki** zóna ablaktáblán jegyezze fel a zóna négy névkiszolgálóját. Ezeket a névkiszolgálókat később fogja használni.

## <a name="create-a-test-record"></a>Tesztrekord létrehozása

Hozzon létre egy **A** rekordot, amelyet tesztelésre használhat. Hozzon létre például egy **www** A rekordot, és állítsa be **10.10.10.10** IP-címmel.

## <a name="create-an-ns-record"></a>NS-rekord létrehozása

Ezután hozzon létre egy névkiszolgálói (NS) rekordot a **mérnöki** zónához.

1. Keresse meg a szülőtartomány zónáját.
2. Kattintson a **+ Rekordhalmaz** gombra.
3. A **Rekordkészlet hozzáadása** ablaktáblán írja be a **tervezést** a **Név** mezőbe.
4. A **Típus mezőben**válassza az **NS**lehetőséget.
5. A **Névkiszolgáló**mezőbe írja be azt a négy névkiszolgálót, amelyet korábban a **mérnöki** zónából rögzített.
6. Kattintson az **OK** gombra.

## <a name="test-the-delegation"></a>A küldöttség tesztelése

Az nslookup segítségével tesztelje a delegálást.

1. Indítson el egy PowerShell-ablakot.
2. A parancssorba írja be a következőt:`nslookup www.engineering.contoso.com.`
3. Nem mérvadó választ kell kapnia, amely a **10.10.10.10**címet mutatja.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogy [miként konfigurálhatja a fordított DNS-t az Azure-ban üzemeltetett szolgáltatásokhoz.](dns-reverse-dns-for-azure-services.md)