---
title: "Tanúsítvány hozzáadása a Java-hitelesítésszolgáltató-tárolójába |} Microsoft Docs"
description: "Útmutató: a tanúsítvány hitelesítésszolgáltatói tanúsítvány hozzáadása a Java-hitelesítésszolgáltató (cacerts) tanúsítványtároló Twilio szolgáltatáshoz vagy Azure Service Bus."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: b6e1a305e19415ab1c4b4c208dac98ad1e2689c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>A tanúsítvány felvétele a Java Hitelesítésszolgáltatói tanúsítványok tárolójában
A következő lépések bemutatják a tanúsítvány hitelesítésszolgáltatói tanúsítvány hozzáadása a Java-hitelesítésszolgáltató tanúsítványtárolóban (cacerts). A használt példa, hogy a Twilio szolgáltatáshoz szükséges a hitelesítésszolgáltató-tanúsítvány. A témakör későbbi részében foglalt információk a Hitelesítésszolgáltatói tanúsítvány telepítése az Azure Service Bus ismerteti. 

Kulcseszköz segítségével hozzáadása előtt a JDK tömörítés és az Azure-projekt hozzáadná a Hitelesítésszolgáltatói tanúsítvány **approot** mappát, vagy egy kulcseszközről felvenni a tanúsítványt használó Azure indítási feladat sikerült futtatása. Ez a példa feltételezi, hogy a CA-tanúsítványokat az éppen zip JDK előtt adhat. Is a példában egy adott CA-tanúsítvány használható, de a lépések egy másik hitelesítésszolgáltató tanúsítvány beszerzése és importálása a cacerts tárolóba hasonló lenne.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>A tanúsítvány felvétele a cacerts tároló
1. Egy rendszergazdai jogú parancssort, amely a JDK értékre van állítva, **jdk\jre\lib\security** mappa, futtassa a következő, hogy mely tanúsítványok telepítették:
   
    `keytool -list -keystore cacerts`
   
    Kérni fogja a tároló jelszót. Az alapértelmezett jelszó **változtatás**. (Ha azt szeretné, hogy módosítsa a jelszavát, a dokumentációban a kulcseszközről: <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Ez a példa feltételezi, hogy a tanúsítvány MD5 ujjlenyomat-67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 nem szerepel a listában, és importálja azt (ezt a tanúsítványt a Twilio API szolgáltatás van szükség).
2. A tanúsítvány beszerzése a listában megtalálható a tanúsítványok [GeoTrust legfelső szintű tanúsítványok](http://www.geotrust.com/resources/root-certificates/). Kattintson a jobb gombbal a tanúsítvány sorozatszámát 35:DE:F4:CF hivatkozásra, majd mentse azokat a **jdk\jre\lib\security** mappa. Ebben a példában a mentett nevű fájlba **Equifax\_biztonságos\_tanúsítvány\_Authority.cer**.
3. Importálja a tanúsítványt a következő parancs segítségével:
   
    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`
   
    Amikor a rendszer megkérdezi, hogy bízzon meg ezt a tanúsítványt a tanúsítvány rendelkezik-e MD5 ujjlenyomat 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, írja be a válasz **y**.
4. Futtassa a következő parancsot a Hitelesítésszolgáltatói tanúsítvány sikeresen importálva lett a biztosításához:
   
    `keytool -list -keystore cacerts`
5. A ZIP-a JDK, és adja hozzá az Azure-projekt **approot** mappát.

Kulcseszköz kapcsolatos információkért lásd: <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Az Azure legfelső szintű tanúsítványok
Az Azure-szolgáltatásokhoz (például az Azure Service Bus) használó alkalmazások Baltimore CyberTrust legfelső szintű tanúsítvány megbízható kell. (2013. április 15 verziótól Azure megkezdte a GTE globális CyberTrust Root telepít át a Baltimore CyberTrust Root. Ez az áttelepítés végrehajtásának befejezéséhez néhány hónapon.)

A tanúsítvány előfordulhat, hogy telepítve legyen a cacerts tárolójában Baltimore ezért fontos, hogy futtassa a **kulcseszköz-lista** parancsot először megtekintéséhez, ha már létezik.

Adja hozzá a Baltimore CyberTrust Root van szüksége, ha rendelkezik sorozatszám 02:00:00:b9 és SHA1 ujjlenyomat d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c: 78:db:28:52:ca:e4:74. Letölthető a <https://cacert.omniroot.com/bc2025.crt>, a rendszer menti a helyi kiterjesztésű **.cer**, majd importálja a használatával **kulcseszköz** a fentiek szerint.

## <a name="next-steps"></a>Következő lépések
A legfelső szintű tanúsítványok Azure általi felhasználási kapcsolatos további információkért lásd: [Azure legfelső szintű tanúsítvány áttelepítési](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Java kapcsolatos további információkért lásd: [Azure Java-fejlesztőknek](/java/azure).

