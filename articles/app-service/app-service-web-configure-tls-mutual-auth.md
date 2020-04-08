---
title: TLS kölcsönös hitelesítés beállítása
description: További információ a TLS-en hitelesített ügyféltanúsítványok hitelesítéséhez. Az Azure App Service az ügyféltanúsítványt elérhetővé teheti az alkalmazáskód számára ellenőrzés céljából.
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.topic: article
ms.date: 10/01/2019
ms.custom: seodec18
ms.openlocfilehash: 2f6dd455024aba184cbb16b5b9c7cfffd032dc70
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811725"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>TLS kölcsönös hitelesítés konfigurálása az Azure App Service-hez

Korlátozhatja az Azure App Service-alkalmazáshoz való hozzáférést, ha különböző típusú hitelesítést engedélyez hozzá. Ennek egyik módja, ha ügyféltanúsítványt kér, ha az ügyfélkérelem túllépi a TLS/SSL-t, és érvényesíti a tanúsítványt. Ezt a mechanizmust TLS kölcsönös hitelesítésnek vagy ügyféltanúsítvány-hitelesítésnek nevezik. Ez a cikk bemutatja, hogyan állíthatja be az alkalmazást az ügyféltanúsítvány-hitelesítés használatára.

> [!NOTE]
> Ha a webhelyet HTTP-n keresztül, nem pedig HTTPS-kapcsolaton keresztül éri el, nem kap ügyféltanúsítványt. Ha tehát az alkalmazás ügyféltanúsítványokat igényel, ne engedélyezze a HTTP-n keresztüli alkalmazásra vonatkozó kérelmeket.
>

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="enable-client-certificates"></a>Ügyféltanúsítványok engedélyezése

Ha úgy szeretné beállítani az alkalmazást, hogy `clientCertEnabled` ügyféltanúsítványokat igényeljen, be kell állítania az alkalmazás beállítását a beállításra. `true` A beállítás beállításához futtassa a következő parancsot a [Cloud Shell](https://shell.azure.com)ben.

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app_name> --resource-group <group_name>
```

## <a name="exclude-paths-from-requiring-authentication"></a>Görbék kizárása a hitelesítést igénylő

Ha engedélyezi a kölcsönös hitelesítést az alkalmazáshoz, az alkalmazás gyökere alatti összes elérési úthoz ügyféltanúsítvány szükséges a hozzáféréshez. Ha azt szeretné, hogy bizonyos elérési utak névtelen hozzáférés esetén nyitva maradjanak, az alkalmazáskonfiguráció részeként kizárási útvonalakat definiálhat.

A kizárási útvonalak konfigurálhatók a **Konfigurációs** > **általános beállítások** kiválasztásával és egy kizárási útvonal meghatározásával. Ebben a példában `/public` az alkalmazás elérési útja alatt lévő akármi nem kér ügyféltanúsítványt.

![Tanúsítványkizárási útvonalak][exclusion-paths]


## <a name="access-client-certificate"></a>Ügyféltanúsítvány elérése

Az App Service-ben a kérelem TLS-végződése a frontend load balancer.In App Service, TLS termination of the request happens at the frontend load balancer. Amikor a kérelmet az alkalmazáskódra továbbítja, ha `X-ARR-ClientCert` az [ügyféltanúsítványok engedélyezve vannak,](#enable-client-certificates)az App Service egy kérelemfejlécet ad meg az ügyféltanúsítvánnyal. Az App Service nem csinál semmit ezzel az ügyféltanúsítvánnyal, csak továbbítja azt az alkalmazásnak. Az alkalmazáskód felelős az ügyféltanúsítvány érvényesítéséért.

Az ASP.NET esetén az ügyféltanúsítvány a **HttpRequest.ClientCertificate** tulajdonságon keresztül érhető el.

Más alkalmazásverem (Node.js, PHP, stb), az ügyfél tanúsítvány érhető el az alkalmazásban egy base64 kódolt érték a `X-ARR-ClientCert` kérelem fejlécében.

## <a name="aspnet-sample"></a>ASP.NET minta

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class Cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Node.js minta

A következő Node.js mintakód leváltja a `X-ARR-ClientCert` fejlécet, és a base64 kódolású PEM-karakterlánctanúsítvány-objektummá történő átalakítására és érvényesítésére [csomópont-kovácsolással](https://github.com/digitalbazaar/forge) konvertálja azt tanúsítványobjektummá:

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```

## <a name="java-sample"></a>Java minta

A következő Java osztály kódolja `X509Certificate` a tanúsítványt egy példányra. `X-ARR-ClientCert` `certificateIsValid()`ellenőrzi, hogy a tanúsítvány ujjlenyomata megegyezik-e a konstruktorban megadottujjlenyomattal, és hogy a tanúsítvány még nem járt le.


```java
import java.io.ByteArrayInputStream;
import java.security.NoSuchAlgorithmException;
import java.security.cert.*;
import java.security.MessageDigest;

import sun.security.provider.X509Factory;

import javax.xml.bind.DatatypeConverter;
import java.util.Base64;
import java.util.Date;

public class ClientCertValidator { 

    private String thumbprint;
    private X509Certificate certificate;

    /**
     * Constructor.
     * @param certificate The certificate from the "X-ARR-ClientCert" HTTP header
     * @param thumbprint The thumbprint to check against
     * @throws CertificateException If the certificate factory cannot be created.
     */
    public ClientCertValidator(String certificate, String thumbprint) throws CertificateException {
        certificate = certificate
                .replaceAll(X509Factory.BEGIN_CERT, "")
                .replaceAll(X509Factory.END_CERT, "");
        CertificateFactory cf = CertificateFactory.getInstance("X.509");
        byte [] base64Bytes = Base64.getDecoder().decode(certificate);
        X509Certificate X509cert =  (X509Certificate) cf.generateCertificate(new ByteArrayInputStream(base64Bytes));

        this.setCertificate(X509cert);
        this.setThumbprint(thumbprint);
    }

    /**
     * Check that the certificate's thumbprint matches the one given in the constructor, and that the
     * certificate has not expired.
     * @return True if the certificate's thumbprint matches and has not expired. False otherwise.
     */
    public boolean certificateIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        return certificateHasNotExpired() && thumbprintIsValid();
    }

    /**
     * Check certificate's timestamp.
     * @return Returns true if the certificate has not expired. Returns false if it has expired.
     */
    private boolean certificateHasNotExpired() {
        Date currentTime = new java.util.Date();
        try {
            this.getCertificate().checkValidity(currentTime);
        } catch (CertificateExpiredException | CertificateNotYetValidException e) {
            return false;
        }
        return true;
    }

    /**
     * Check the certificate's thumbprint matches the given one.
     * @return Returns true if the thumbprints match. False otherwise.
     */
    private boolean thumbprintIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        MessageDigest md = MessageDigest.getInstance("SHA-1");
        byte[] der = this.getCertificate().getEncoded();
        md.update(der);
        byte[] digest = md.digest();
        String digestHex = DatatypeConverter.printHexBinary(digest);
        return digestHex.toLowerCase().equals(this.getThumbprint().toLowerCase());
    }

    // Getters and setters

    public void setThumbprint(String thumbprint) {
        this.thumbprint = thumbprint;
    }

    public String getThumbprint() {
        return this.thumbprint;
    }

    public X509Certificate getCertificate() {
        return certificate;
    }

    public void setCertificate(X509Certificate certificate) {
        this.certificate = certificate;
    }
}
```

[exclusion-paths]: ./media/app-service-web-configure-tls-mutual-auth/exclusion-paths.png
