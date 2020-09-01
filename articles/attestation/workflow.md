---
title: Azure-igazolási munkafolyamat
description: Az Azure-igazolás munkafolyamata.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 240b27f897d8e7a34026701cf7fdc844eb9d4086
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237268"
---
# <a name="workflow"></a>Munkafolyamat

Microsoft Azure igazolás igazolja az enklávétól, és kiértékeli az Azure biztonsági alapkonfiguráció és a konfigurálható szabályzatok elleni bizonyítékokat. A sikeres ellenőrzés az Azure-igazolás igazoló jogkivonatot hoz létre az enklávé megbízhatóságának megerősítéséhez.

Az Azure igazolási munkafolyamatában a következő szereplők vesznek részt:

- **Függő entitás**: az az összetevő, amely az Azure-igazolásra támaszkodik az enklávé érvényességének ellenőrzéséhez. 
- **Ügyfél**: az a komponens, amely adatokat gyűjt egy enklávéból, és kéréseket küld az Azure-igazolásnak. 
- **Azure-tanúsítvány**: az a komponens, amely az ügyféltől származó enklávé-bizonyítékokat fogad el, ellenőrzi, és visszaadja a tanúsító jogkivonatot az ügyfélnek.


## <a name="enclave-validation-work-flow"></a>Enklávé ellenőrzési munkafolyamata

Az alábbi általános lépések egy tipikus SGX ENKLÁVÉHOZ enklávé-igazolási munkafolyamatban (az Azure igazolás használatával):

1. Az ügyfél egy enklávéból származó bizonyítékot gyűjt. A bizonyítékok az enklávé környezetére és az enklávéban futó ügyféloldali könyvtárra vonatkozó információk.
1. Az ügyfél rendelkezik egy URI-val, amely az Azure-igazolás egy példányára hivatkozik. Az ügyfél hitelesíti magát az Azure AD-ben, és beolvas egy hozzáférési jogkivonatot.
1. Az ügyfél a hozzáférési jogkivonattal együtt bizonyítékot küld az Azure-igazolásra. A szolgáltatónak küldött pontos információk az enklávé típusától függenek.
1. Az Azure-igazolás ellenőrzi a beküldött adatokat, és kiértékeli azt egy konfigurált házirend alapján. Ha az ellenőrzés sikeres, az Azure igazolása hibát jelez, és visszaadja az ügyfélnek. Ha ez a lépés meghiúsul, az Azure-igazolás hibát jelez az ügyfélnek. 
1. Az ügyfél elküldi az igazolási jogkivonatot a függő entitásnak. A függő entitás meghívja az Azure-tanúsítvány nyilvános kulcsú metaadat-végpontját az aláíró tanúsítványok lekéréséhez. A függő entitás ezután ellenőrzi az igazolási jogkivonat aláírását, és biztosítja az enklávé megbízhatóságát. 

![Enklávé ellenőrzési folyamata](./media/validation-flow.png)


## <a name="next-steps"></a>Következő lépések
- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az Azure-igazolás beállítása a PowerShell használatával](quickstart-powershell.md)
