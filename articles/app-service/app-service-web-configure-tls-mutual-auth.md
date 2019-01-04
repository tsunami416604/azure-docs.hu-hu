---
title: TLS kölcsönös hitelesítés – az Azure App Service konfigurálása
description: Ismerje meg, hogyan állítsa be alkalmazását a TLS ügyfél Tanúsítványalapú hitelesítés használatára.
services: app-service
documentationcenter: ''
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
ms.custom: seodec18
ms.openlocfilehash: d441329bc3f279e95b2ee302db53d78f786c3470
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650397"
---
# <a name="how-to-configure-tls-mutual-authentication-for-azure-app-service"></a>TLS kölcsönös hitelesítés az Azure App Service konfigurálása
## <a name="overview"></a>Áttekintés
Engedélyezi a hitelesítést, a különböző típusú hozzáférést korlátozhatja az Azure App Service-alkalmazás. Ennek egyik módja a hitelesítés ügyféltanúsítvány használatával, ha a rendszer a TLS/SSL-en keresztül. Ez a mechanizmus TLS kölcsönös hitelesítés vagy a hitelesítést, és ez a cikk részletesen ügyféltanúsítvány-alapú hitelesítés használata az alkalmazás beállítása ügyféltanúsítvány nevezzük.

> **Megjegyzés:** Ha a HTTP és HTTPS-nem keresztül éri el a hely, nem fog kapni minden olyan ügyfél-tanúsítványt. Tehát ha az alkalmazás ügyfél-tanúsítványok nem engedélyezze kérelmeket az alkalmazás HTTP-n keresztül.
> 
> 

## <a name="configure-app-service-for-client-certificate-authentication"></a>Az App Service az ügyféltanúsítvány-alapú hitelesítés konfigurálása
Az alkalmazás beállítása az ügyféltanúsítványok megköveteléséhez, kell adja hozzá az alkalmazás az ügyféltanúsítvány engedélyezésével hely beállítást, és állítsa igaz értékre. A beállítás akkor is konfigurálhatók, az SSL-tanúsítványok panel alatt az Azure Portalon.

Használhatja a [ARMClient eszköz](https://github.com/projectkudu/ARMClient) megkönnyíti a REST API-hívás írhat. Miután jelentkezik be az eszközt, szüksége lesz az alábbi parancsot:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

cserélje le a tartalmát {} való az alkalmazás és a tartalom nevű enableclientcert.json az alábbi JSON-fájl létrehozásával kapcsolatos információkat:

    {
        "location": "My App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

Ellenőrizze, hogy a "hely" értékét módosítsa arra, bárhol is legyenek az alkalmazás található példa, USA északi középső Régiója és USA nyugati RÉGIÓJA stb.

Is https://resources.azure.com tükrözés, a `clientCertEnabled` tulajdonságot `true`.

> **Megjegyzés:** Ha ARMClient futtatja a Powershell, szüksége lesz karaktert a \@ a JSON-fájlt egy vissza osztásjelek a szimbólum ".
> 
> 

## <a name="accessing-the-client-certificate-from-app-service"></a>Az ügyféltanúsítvány elérése az App Service-ben
Használja az ASP.NET, és állítsa be alkalmazását az ügyféltanúsítvány-alapú hitelesítés használatára, ha a tanúsítvány érhető el a **HttpRequest.ClientCertificate** tulajdonság. Más alkalmazáscsoportokat az alkalmazásban a "X-ARR-ClientCert" kérés fejlécében base64-kódolású értéket keresztül elérhető lesz az ügyféltanúsítvány. Az alkalmazás is hozzon létre egy tanúsítványt a ezt az értéket, majd az alkalmazás hitelesítési és engedélyezési célból.

## <a name="special-considerations-for-certificate-validation"></a>Különleges szempontok a tanúsítvány érvényesítése
Az ügyféltanúsítvány, amelyet az alkalmazás elküld nem halad át minden érvényesítése az Azure App Service platformon. Ez a tanúsítvány érvényesítése feladata az alkalmazás. Itt látható minta ASP.NET-kód, amely ellenőrzi a hitelesítési tanúsítvány tulajdonságai.

    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
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

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }
