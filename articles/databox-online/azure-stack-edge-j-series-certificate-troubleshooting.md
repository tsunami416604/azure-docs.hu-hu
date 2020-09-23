---
title: Tanúsítványok hibaelhárítása Azure Stack Edge Pro-val GPU-val | Microsoft Docs
description: A Azure Stack Edge Pro GPU-eszközzel kapcsolatos hibák elhárítását ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: a0918c6cebd50231a9664811bb467e04d2d2bfd9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891324"
---
# <a name="troubleshooting-certificate-errors"></a>Tanúsítványokkal kapcsolatos hibák elhárítása

A cikk a tanúsítványoknak a Azure Stack Edge Pro-eszközre történő telepítésekor gyakori hibák elhárítását ismerteti.

## <a name="common-certificate-errors"></a>Gyakori tanúsítvány-hibák

Az alábbi táblázat a hibákkal és a lehetséges megoldásokkal kapcsolatos gyakori tanúsítvány-hibákat és részletes információkat mutatja be:

> [!NOTE]
> &#8220;{0} , {1} ,..., {n} &#8221; a pozíciós paramétereket jelölik. A pozicionális paraméterek a használt tanúsítványtól függően értékeket vesznek fel.

| Hibakód | Leírás |
|---|---|
| CertificateManagement_UntrustedCertificate | A tulajdonos nevű tanúsítványhoz tartozó tanúsítványlánc {0} megszakadt. Töltse fel az aláíró lánc tanúsítványát a tanúsítvány feltöltése előtt.|
| CertificateManagement_DeviceNotRegistered| Az eszköz nincs aktiválva. A támogatási tanúsítványokat csak az aktiválás után töltheti fel.|
| CertificateManagement_EmptySAN | A tulajdonos nevű tanúsítvány {0} nem rendelkezik tulajdonos alternatív nevével. A tanúsítvány tulajdonságainak megtekintése és új tanúsítvány beolvasása.|
| CertificateManagement_ExpiredCertificate | A típusú tanúsítvány érvényessége {0} lejárt vagy hamarosan lejár. Ellenőrizze a tanúsítvány lejáratát, és ha szükséges, egy új tanúsítványt.|
| CertificateManagement_FormatMismatch | A tanúsítvány formátuma nem támogatott. Ellenőrizze a tanúsítvány formátumát, és ha szükséges, hozza egy új tanúsítványt.  {0}A rendszer a következőt várta: {1} . |
| CertificateManagement_GenericError | Nem hajtható végre a Tanúsítványkezelő művelet. Próbálja megismételni a műveletet néhány perc múlva. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| CertificateManagement_HttpsBindingFailure | A tulajdonos nevű tanúsítvány {0} nem tudott biztonságos HTTPS-csatornát létrehozni. Ellenőrizze a feltöltött tanúsítványt, és ha szükséges, új tanúsítványt kell behoznia. Ez a hiba az eszköz csomópont-tanúsítványával fordul elő.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezhet a kulcshasználat tanúsítványának aláírásával. Ellenőrizze a tanúsítvány kulcshasználat használatát, és ha szükséges, egy új tanúsítványt. Ez a hiba az aláíró lánc tanúsítványával fordul elő. |
| CertificateManagement_IncorrectKeyNumber | A tulajdonos nevével rendelkező tanúsítvány {0} nem megfelelő számú kulccsal rendelkezik {1} . Ellenőrizze a tanúsítvány kulcsának számát, és ha szükséges, egy új tanúsítványt.|
| CertificateManagement_InvalidP7b | A feltöltött tanúsítványfájl érvénytelen.  Ellenőrizze a tanúsítvány formátumát, és ha szükséges, hozza egy új tanúsítványt.|
| CertificateManagement_KeyAlgorithmNotRSA | Ez a tanúsítvány nem használja az RSA-kulcs algoritmusát. Csak az RSA-tanúsítványok támogatottak. |
| CertificateManagement_KeySizeNotSufficient | A tulajdonos nevével rendelkező tanúsítvány {0} mérete nem elegendő {1} . A minimális kulcs mérete 4096.|
| CertificateManagement_MissingClientOid | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezik ügyfél-hitelesítési OID azonosítóval. Ellenőrizze a tanúsítvány tulajdonságait, és ha szükséges, egy új tanúsítványt.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezik a kulcshasználat digitális aláírásával. Ellenőrizze a tanúsítvány tulajdonságait, és ha szükséges, egy új tanúsítványt. |
| CertificateManagement_MissingEntryInSAN | A tulajdonos nevű tanúsítvány {0} nem rendelkezik tulajdonos neve bejegyzéssel a tulajdonos alternatív nevében. A tanúsítvány tulajdonságainak megtekintése és új tanúsítvány beolvasása. |
| CertificateManagement_MissingKeyCertSignKeyUsage | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezik a kulcshasználat tanúsítvány-aláírással. Ellenőrizze a tanúsítvány tulajdonságait, és ha szükséges, egy új tanúsítványt.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezik kulcs-titkosítási. Ellenőrizze a tanúsítvány tulajdonságait, és ha szükséges, egy új tanúsítványt. |
| CertificateManagement_MissingServerOid | A tulajdonos nevével rendelkező tanúsítvány {0} nem rendelkezik kiszolgálói hitelesítés OID-vel. Ellenőrizze a tanúsítvány tulajdonságait, és ha szükséges, egy új tanúsítványt.|
| CertificateManagement_NameMismatch | A tanúsítvány típusa nem egyezik. Várt hatókör: {0} , található {1} . Töltse fel a megfelelő tanúsítványt.|
| CertificateManagement_NoPrivateKeyPresent | A tulajdonos névvel rendelkező tanúsítvány {0} nem tartalmaz titkos kulcsot. Töltsön fel egy titkos kulccsal rendelkező. pfx-tanúsítványt.|
| CertificateManagement_NotSelfSignedCertificate | A tulajdonos nevű tanúsítvány {0} nem saját aláírású. A főtanúsítványoknak önaláírtnak kell lenniük |
| CertificateManagement_NotSupportedOnVirtualAppliance | Ez a művelet nem támogatott a virtuális eszközön. Ez a hiba azt jelzi, hogy az aláírás csak a taktikai felhőalapú berendezésben futó Data Box Gateway esetén jelentkezik. Ez a hiba akkor fordul elő, amikor az eszközt a Windows PowerShellen keresztül kezeli.|
| CertificateManagement_SelfSignedCertificate | A tulajdonos nevű tanúsítvány {0} önaláírt. A megfelelő aláírással rendelkező tanúsítvány feltöltése.|
| CertificateManagement_SignatureAlgorithmSha1 | A tulajdonos nevű tanúsítvány {0} nem támogatott aláírási algoritmust tartalmaz. Az SHA1-RSA nem támogatott. |
| CertificateManagement_SubjectNamesInvalid | A tulajdonos nevével rendelkező tanúsítványhoz {0} nem tartozik a tanúsítvány helyes tulajdonosának neve vagy a tulajdonos alternatív neve {1} . Ellenőrizze a feltöltött tanúsítványt, és ha szükséges, új tanúsítványt kell behoznia. Azt is ellenőriznie kell, hogy a DNS-név megegyezik-e a SANS-nevekkel.|
| CertificateManagement_UnreadableCertificate | A típusú tanúsítvány {0} nem olvasható. Ez a hiba akkor fordul elő, ha a tanúsítvány nem olvasható vagy sérült. Új tanúsítvány létrehozása.|
| CertificateSubjectNotFound | Nem található a tulajdonos nevű tanúsítvány {0} . Új tanúsítvány létrehozása.|

## <a name="next-steps"></a>Következő lépések

[Tanúsítványokra vonatkozó követelmények](azure-stack-edge-j-series-certificate-requirements.md)