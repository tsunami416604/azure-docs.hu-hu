---
title: A Key Vault elérése tűzfal mögül | Microsoft Docs
description: Információk a Key Vault tűzfal mögötti alkalmazásokból történő eléréséről
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/13/2016
ms.author: ambapat

---
# A Key Vault elérése tűzfal mögül
### Kérdés: A Key Vault-ügyfélalkalmazásomnak tűzfal mögött kell lennie. Milyen portokat/gazdagépeket/IP-címeket kell megnyitnom a Key Vaulthoz való hozzáférés engedélyezéséhez?
A Key Vault eléréséhez a Key Vault-ügyfélalkalmazásnak a különféle funkciók biztosításához képesnek kell lennie több végpont elérésére.

* Hitelesítés (az Azure Active Directory használatával)
* A Key Vault felügyelete (amelynek része a hozzáférési házirendek létrehozása/olvasása/frissítése/törlése, illetve beállítása) az Azure Resource Manager használatával
* A magában a Key Vaultban tárolt objektumok (kulcsok és titkos kulcsok) elérése és kezelése Key Vault speciális végpontján (pl. [https://sajattaroloneve.vault.azure.net](https://yourvaultname.vault.azure.net)) keresztül zajlik.  

A konfigurációtól és a környezettől függően lehetnek bizonyos eltérések.   

## Portok
Mindhárom funkció (hitelesítés, felügyeleti és az adatsíkhoz való hozzáférés) Key Vault felé irányuló összes forgalma a 443-as HTTPS-porton keresztül zajlik. A CRL használata esetén azonban alkalmanként HTTP-forgalom is előfordul (a 80-as porton). Az OCSP protokollhoz hozzáférő ügyfeleknek nem szabad elérniük a CRL-t, azonban alkalmanként elérhetik [http://cdp1.public-trust.com/CRL/Omniroot2025.crl](http://cdp1.public-trust.com/CRL/Omniroot2025.crl) címet.  

## Authentication
A Key Vault-ügyfélalkalmazásnak a hitelesítés érdekében hozzá kell férnie az Azure Active Directory-végpontokhoz. A használt végpont függ az AAD bérlői konfigurációjától, valamint a név típusától (felhasználói név, szolgáltatásnév), illetve a fiók típusától (pl. Microsoft-fiók vagy szervezeti azonosító).  

| Résztvevő típusa | Végpont:port |
| --- | --- |
| A Microsoft-fiókot használó felhasználó<br> (pl. felhasznalo@hotmail.com) |**Globálisan:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Amerikai Egyesült Államok kormánya által használt Azure:**<br> login-us.microsoftonline.com:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443<br><br> és <br>login.live.com:443 |
| Felhasználói/szolgáltatásnév szervezeti azonosító és AAD használatával (például user@contoso.com) |**Globálisan:**<br> login.microsoftonline.com:443<br><br> **Azure China:**<br> login.chinacloudapi.cn:443<br><br>**Amerikai Egyesült Államok kormánya által használt Azure:**<br> login-us.microsoftonline.com:443<br><br>**Azure Germany:**<br> login.microsoftonline.de:443 |
| Szervezeti azonosítót + AD FS-t vagy más összevont végpontot használó felhasználói/szolgáltatásnév (pl. felhasznalo@contoso.com) |Szervezeti azonosító és AD FS vagy más összevont végpontok esetén az összes fenti végpont |

Más összetett forgatókönyvek is előfordulhatnak. További információkért tekintse meg az [Azure Active Directory hitelesítési folyamatát](/documentation/articles/active-directory-authentication-scenarios/), az [alkalmazások Azure Active Directoryval való integrálását](/documentation/articles/active-directory-integrating-applications/) és [az Active Directory hitelesítési protokolljait ismertető cikket](https://msdn.microsoft.com/library/azure/dn151124.aspx).  

## A Key Vault felügyelete
A Key Vault felügyeletéhez (CRUD és hozzáférési házirend beállítása) a Key Vault-ügyfélalkalmazásnak el kell érnie az Azure Resource Manager-végpontot.  

| Művelet típusa | Végpont:port |
| --- | --- |
| Key Vault vezérlési síkjával végzett műveletek<br> az Azure Resource Manager használatával |**Globálisan:**<br> management.azure.com:443<br><br> **Azure China:**<br> management.chinacloudapi.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 |
| Azure Active Directory – Graph API |**Globálisan:**<br> graph.windows.net:443<br><br> **Azure China:**<br> graph.chinacloudapi.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> graph.windows.net:443<br><br> **Azure Germany:**<br> graph.cloudapi.de:443 |

## Key Vault-műveletek
Az összes Key Vault-objektummal (kulcsok és titkos kulcsok) végzett felügyeleti és titkosítási művelethez a Key Vault-ügyfélnek el kell érnie a Key Vault-végpontot. A végpont DNS-utótagja a Key Vault helyétől függően eltér. A Key Vault-végpont formátuma az alábbi táblázatban látható módon <tároló-neve>.<területspecifikus-dns-utótag>.  

| Művelet típusa | Végpont:port |
| --- | --- |
| Key Vault-műveletek, például a kulcsokon végzett titkosítási műveletek, a kulcsok és titkos kulcsok létrehozása/olvasása/frissítése/törlése, valamint a címkék és egyéb attribútumok beállítása/lekérése a Key Vault-objektumokon |**Globálisan:**<br> &lt;tároló-neve&gt;.vault.azure.net:443<br><br> **Azure China:**<br> &lt;tároló-neve&gt;.vault.azure.cn:443<br><br> **Amerikai Egyesült Államok kormánya által használt Azure:**<br> &lt;tároló-neve&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;tároló-neve&gt;.vault.microsoftazure.de:443 |

## IP-címtartományok
A Key Vault szolgáltatás pedig más Azure-erőforrásokat vesz igénybe (pl. a SaaS-infrastruktúrát), ezért nem lehet olyan megadott IP-címtartományokat meghatározni, amelyet a Key Vault szolgáltatásvégpontja bármely adott pillanatban használ. Ha azonban a tűzfal csak az IP-címtartományokat támogatja, akkor tekintse meg a [Microsoft Azure adatközpont IP-címtartományait bemutató cikket](https://www.microsoft.com/download/details.aspx?id=41653).   A hitelesítéshez és az identitásszolgáltatáshoz (Azure Active Directory) az alkalmazásnak képesnek kell lennie kapcsolódni a [hitelesítési és identitáscímeket ismertető cikkben](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) leírt végpontokhoz.

## Következő lépések
* Amennyiben a Key Vault szolgáltatással kapcsolatos kérdése merülne fel, tekintse az [Azure Key Vault fórumait](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

<!--HONumber=Sep16_HO4-->


