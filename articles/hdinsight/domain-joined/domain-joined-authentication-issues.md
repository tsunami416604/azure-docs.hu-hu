---
title: Hitelesítési problémák az Azure HDInsight
description: Hitelesítési problémák az Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.openlocfilehash: 2ffc3ced360e1fdf00f69ea5826e6c6af7806f71
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215993"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Hitelesítési problémák az Azure HDInsight

Ez a cikk az Azure HDInsight-fürtökkel való interakció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

A Azure Data Lake (Gen1 vagy Gen2) által támogatott biztonságos fürtökön, amikor a tartományi felhasználók a HDI-átjárón keresztül jelentkeznek be a fürtszolgáltatásba (például az Apache Ambari portálra való bejelentkezéshez), a HDI-átjáró először a Azure Active Directory (Azure AD) OAuth-tokenjét próbálja meg beolvasni. , majd szerezzen be egy Kerberos-jegyet az Azure AD DSból. A hitelesítés bármelyik szakaszban sikertelen lehet. Ez a cikk a problémák némelyikének hibakeresését célozza meg.

Ha a hitelesítés sikertelen, a rendszer kérni fogja a hitelesítő adatok megadását. Ha megszakítja ezt a párbeszédpanelt, a rendszer kinyomtatja a hibaüzenetet. Íme néhány gyakori hibaüzenet:

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant vagy unauthorized_client, 50126

### <a name="issue"></a>Probléma

Sikertelen volt a bejelentkezés az összevont felhasználók számára a 50126 hibakódgal (a bejelentkezés sikeres a Cloud Users esetében). A hibaüzenet a következőhöz hasonló:

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>Ok

Az Azure AD 50126-es hibakód azt jelenti, hogy a bérlő nem állította be a `AllowCloudPasswordValidation` szabályzatot.

### <a name="resolution"></a>Megoldás:

Az Azure ad-bérlő vállalati rendszergazdája engedélyezheti az Azure AD számára a jelszó-kivonatok használatát az ADFS-t használó felhasználók számára.  Alkalmazza a `AllowCloudPasswordValidationPolicy` az [Enterprise Security Package használata a HDInsight-ben](../domain-joined/apache-domain-joined-architecture.md)című cikkben látható módon.

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant vagy unauthorized_client, 50034

### <a name="issue"></a>Probléma

A bejelentkezés sikertelen a 50034-es hibakód miatt. A hibaüzenet a következőhöz hasonló:

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>Ok

A Felhasználónév helytelen (nem létezik). A felhasználó nem ugyanazt a felhasználónevet használja, mint amelyet a Azure Portal használ.

### <a name="resolution"></a>Megoldás:

Használja ugyanazt a felhasználónevet, amely az adott portálon működik.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant vagy unauthorized_client, 50053

### <a name="issue"></a>Probléma

A felhasználói fiók ki van zárva, hibakód: 50053. A hibaüzenet a következőhöz hasonló:

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>Ok

Túl sok bejelentkezési kísérlet helytelen jelszóval.

### <a name="resolution"></a>Megoldás:

Várjon 30 percet, és állítsa le a hitelesíteni próbált alkalmazásokat.

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant vagy unauthorized_client, 50053

### <a name="issue"></a>Probléma

A jelszó lejárt, hibakód: 50053. A hibaüzenet a következőhöz hasonló:

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>Ok

A jelszó lejárt.

### <a name="resolution"></a>Megoldás:

Módosítsa a jelszót a Azure Portalban (a helyszíni rendszeren), majd várjon 30 percet, amíg a szinkronizálás befejeződik.

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>Probléma

A rendszer hibaüzenetet kap `interaction_required`.

### <a name="cause"></a>Ok

A rendszer szerint feltételes hozzáférési szabályzat vagy MFA vonatkozik a felhasználóra. Mivel az interaktív hitelesítés még nem támogatott, a felhasználót vagy a fürtöt ki kell venni az MFA/feltételes hozzáférés hatálya alól. Ha a fürt kivételét választja (IP-cím alapú kivételi szabályzat), akkor győződjön meg arról, hogy az AD `ServiceEndpoints` engedélyezve vannak az adott vnet.

### <a name="resolution"></a>Megoldás:

Használja a feltételes hozzáférési házirendet, és a HDInisght-fürtöket az MFA-ból is felhasználhatja, ahogy [azt a Enterprise Security Package Azure Active Directory Domain Services használatával történő konfigurálása](./apache-domain-joined-configure-using-azure-adds.md)című rész mutatja.

---

## <a name="sign-in-denied"></a>Bejelentkezés megtagadva

### <a name="issue"></a>Probléma

A bejelentkezés megtagadva.

### <a name="cause"></a>Ok

Ennek a szakasznak a beszerzéséhez a OAuth-hitelesítés nem jelent problémát, de a Kerberos-hitelesítés is. Ha a fürtöt a ADLS támogatja, a OAuth-bejelentkezés sikeres volt a Kerberos-hitelesítés megkísérlése előtt. A WASB-fürtökön a OAuth bejelentkezés nem történt meg. Számos oka lehet a Kerberos-hibák, például a jelszó-kivonatok szinkronizálása, a felhasználói fiók kizárva az Azure AD DS, és így tovább. A jelszó-kivonatok szinkronizálása csak akkor történt meg, amikor a felhasználó megváltoztatja a jelszót. Az Azure AD DS példány létrehozásakor a rendszer elindítja a létrehozás után módosított jelszavak szinkronizálását. Nem fogja visszamenőlegesen szinkronizálni azokat a jelszavakat, amelyek a kezdetektől fogva lettek beállítva.

### <a name="resolution"></a>Megoldás:

Ha úgy gondolja, hogy a jelszavak nem szinkronizálhatók, próbálja meg módosítani a jelszót, és várjon néhány percet.

Próbálja ki az SSH-t a-be, hogy a tartományhoz csatlakoztatott gépről ugyanazzal a felhasználói hitelesítő adatokkal próbálja meg hitelesíteni a hitelesítést (kinit parancsot). SSH-t a Head/Edge csomópontba egy helyi felhasználóval, majd futtassa a kinit parancsot.

---

## <a name="kinit-fails"></a>a kinit parancsot sikertelen

### <a name="issue"></a>Probléma

A kinit parancsot sikertelen.

### <a name="cause"></a>Ok

Változik.

### <a name="resolution"></a>Megoldás:

Ahhoz, hogy a kinit parancsot sikeres legyen, ismernie kell a `sAMAccountName` (ez a fiók neve a tartomány nélkül). `sAMAccountName` általában a fiók előtagja (például a `bob@contoso.com`). Egyes felhasználók esetében eltérő lehet. A `sAMAccountName`megismeréséhez szükség van a címtár tallózására és keresésére.

`sAMAccountName`megkeresésének módjai:

* Ha a helyi Ambari-rendszergazda használatával tud bejelentkezni a Ambari-be, tekintse meg a felhasználók listáját.

* Ha rendelkezik [tartományhoz csatlakozó Windows-géppel](../../active-directory-domain-services/manage-domain.md), a szabványos Windows ad-eszközök használatával böngészhet. Ehhez a tartományban működő fiókra van szükség.

* A fő csomóponton a SAMBA-parancsokat használhatja a kereséshez. Ehhez érvényes Kerberos-munkamenetre (sikeres kinit parancsot) van szükség. NET ADS-keresés (userPrincipalName = Bob *)

    A keresés/Tallózás eredménynek meg kell jelennie a `sAMAccountName` attribútumnak. Emellett megtekintheti a többi olyan attribútumot is, mint például a `pwdLastSet`, `badPasswordTime`, `userPrincipalName` stb.).

---

## <a name="kinit-fails-with-preauthentication-failure"></a>a kinit parancsot sikertelen előhitelesítés esetén

### <a name="issue"></a>Probléma

A kinit parancsot `Preauthentication` hibával meghiúsul.

### <a name="cause"></a>Ok

Helytelen Felhasználónév vagy jelszó.

### <a name="resolution"></a>Megoldás:

Keresse meg a felhasználónevet és a jelszót. Tekintse meg a fent ismertetett egyéb tulajdonságokat is. A részletes hibakeresés engedélyezéséhez futtassa `export KRB5_TRACE=/tmp/krb.log` a munkamenetből a kinit parancsot kipróbálása előtt.

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>A feladatok/HDFS parancs TokenNotFoundException miatt meghiúsul

### <a name="issue"></a>Probléma

A feladatok/HDFS parancs `TokenNotFoundException`miatt meghiúsul.

### <a name="cause"></a>Ok

A szükséges OAuth hozzáférési jogkivonat nem található a sikeres művelethez/parancshoz. A ADLS/ABFS-illesztőprogram megpróbálja lekérni a OAuth hozzáférési tokent a hitelesítőadat-szolgáltatásból a tárolási kérelmek végrehajtása előtt. Ez a jogkivonat akkor lesz regisztrálva, ha ugyanazzal a felhasználóval jelentkezik be a Ambari-portálra.

### <a name="resolution"></a>Megoldás:

Győződjön meg arról, hogy sikeresen bejelentkezett a Ambari-portálra azon a felhasználónévn keresztül, amelynek az identitását a rendszer a feladatok futtatására használja.

---

## <a name="error-fetching-access-token"></a>Hiba a hozzáférési jogkivonat beolvasásakor

### <a name="issue"></a>Probléma

A felhasználó hibaüzenetet kap `Error fetching access token`.

### <a name="cause"></a>Ok

Ez a hiba időnként fordul elő, amikor a felhasználók ACL-ek használatával próbálnak hozzáférni a ADLS Gen2hoz, és a Kerberos-jogkivonat lejárt.

### <a name="resolution"></a>Megoldás:

* Azure Data Lake Storage Gen1 a böngésző gyorsítótárát, és jelentkezzen be újra a Ambari.

* Azure Data Lake Storage Gen2 esetén futtassa a `/usr/lib/hdinsight-common/scripts/RegisterKerbWithOauth.sh <upn>`t azon felhasználó számára, akit a felhasználó a következőként próbál bejelentkezni:

---

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon a [@AzureSupporthoz](https://twitter.com/azuresupport) – a hivatalos Microsoft Azure fiókot a felhasználói élmény javításához. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)ismertető témakört. Az előfizetés-kezeléssel és számlázással kapcsolatos támogatás a Microsoft Azure-előfizetés részét képezi, míg a technikai támogatást [Azure-támogatási csomagjainkkal](https://azure.microsoft.com/support/plans/) biztosítjuk.
