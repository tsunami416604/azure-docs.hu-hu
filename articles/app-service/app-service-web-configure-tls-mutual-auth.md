---
title: "TLS kölcsönös hitelesítés beállítása webalkalmazáshoz"
description: "Útmutató: a webalkalmazás a TLS ügyfél Tanúsítványalapú hitelesítés használatára konfigurálja."
services: app-service
documentationcenter: 
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
ms.openlocfilehash: db69852cffd1ff331ac4a640b04ea4360d00bf75
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>TLS kölcsönös hitelesítés beállítása webalkalmazáshoz
## <a name="overview"></a>Áttekintés
Hozzáférés engedélyezése különböző hitelesítési az korlátozhatja az Azure-webalkalmazásban. Egy módja, hogy hitelesítse a használ a tanúsítványt, ha a kérelem a TLS/SSL kapcsolaton keresztül. A mechanizmus TLS kölcsönös hitelesítés vagy ügyféltanúsítvány-hitelesítést, és ez a cikk részletesen ismerteti ügyféltanúsítvány-alapú hitelesítés használatához a webalkalmazás beállítása hogyan nevezzük.

> **Megjegyzés:** a webhely HTTP és HTTPS protokollt használó nem fér hozzá, addig nem kap minden ügyfél-tanúsítványt. Így ha az alkalmazás ügyfél-tanúsítványok nem engedélyezze kérelmek az alkalmazás HTTP Protokollon keresztül.
> 
> 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="configure-web-app-for-client-certificate-authentication"></a>Ügyféltanúsítvány-alapú hitelesítés a webalkalmazás konfigurálása
Adja hozzá a clientCertEnabled beállítása a webalkalmazás, majd állítsa be true értékre kell Ügyféltanúsítványok megkövetelése a webalkalmazás beállítása. Ez a beállítás már nem érhető el a kezelhetőséget, a portálon keresztül, és a REST API-t kell végrehajtásához használható.

Használhatja a [ARMClient eszköz](https://github.com/projectkudu/ARMClient) megkönnyítheti a REST API-hívás létrehozható. Az eszköz bejelentkezés után szüksége lesz az alábbi parancsot:

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

cseréje mindent megkeresése és a webalkalmazás vonatkozó információkat, és a fájl létrehozásakor a következő nevű enableclientcert.json a következő JSON a tartalom:

    {
        "location": "My Web App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

Ügyeljen arra, hogy módosítsa az értéket a "hely" mindig a webalkalmazás helyezkedik pl. északi középső Régiójában vagy nyugati USA stb.

A tükrözés https://resources.azure.com is használhatja a `clientCertEnabled` tulajdonságot `true`.

> **Megjegyzés:** ARMClient Powershell futtatja, ha szüksége lesz karakterláncot a @ jel hátsó osztásjelek rendelkező JSON-fájl ".
> 
> 

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Az ügyféltanúsítványt a webes alkalmazás elérése
Ha ASP.NET használ, és állítsa be alkalmazását ügyféltanúsítvány-alapú hitelesítés használatára, a tanúsítvány érhető el a **HttpRequest.ClientCertificate** tulajdonság. Más alkalmazás csomagokat az ügyfél-tanúsítvány az alkalmazás base64-kódolású értéket az "X-ARR-ClientCert" kérelem fejlécében keresztül elérhető lesz. Az alkalmazás hozzon létre egy tanúsítványt az ezt az értéket, és majd a hitelesítés és engedélyezés céljából az alkalmazás használatával.

## <a name="special-considerations-for-certificate-validation"></a>Különleges szempontok a tanúsítvány érvényesítése
Az ügyféltanúsítványt, az alkalmazásnak küldött nem halad át egyetlen ellenőrzési az Azure Web Apps platformon. Ez a tanúsítvány érvényesítése feladata a webalkalmazás. Itt található ASP.NET mintakód, amely ellenőrzi a tanúsítvány tulajdonságai hitelesítési célokra.

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
