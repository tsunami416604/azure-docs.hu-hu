---
title: Csatlakozás Azure Analysis Services kiszolgálókhoz | Microsoft Docs
description: Útmutató az Azure-beli Analysis Services-kiszolgálóról való kapcsolódáshoz és az adatok lekéréséhez.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3231b46060cbb755ada000473c8fbe873cc51ef1
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73147311"
---
# <a name="connecting-to-servers"></a>Kapcsolódás kiszolgálókhoz

Ez a cikk a kiszolgálókhoz való csatlakozást ismerteti adatmodellezési és-kezelési alkalmazások, például a SQL Server Management Studio (SSMS) vagy a Visual Studio Analysis Services-projektekkel, valamint a Microsoft Excelben, például a Microsoft Excel alkalmazással Power BI Asztali vagy egyéni alkalmazások. A Azure Analysis Serviceshoz való csatlakozás HTTPS protokollt használ.

## <a name="client-libraries"></a>Klienskódtárak

[A legújabb ügyféloldali kódtárak beszerzése](analysis-services-data-providers.md)

A kiszolgálóhoz való minden kapcsolat, a típustól függetlenül, frissített AMO-, ADOMD.NET-és OLEDB-ügyféloldali kódtárak szükségesek a Analysis Services-kiszolgálóval való csatlakozáshoz és az ahhoz való kapcsolódáshoz. A SSMS, a Visual studióhoz, az Excel 2016-es és újabb verzióihoz, valamint Power BI a legújabb ügyféloldali kódtárakat a rendszer a havi kiadásokkal telepíti vagy frissíti. Bizonyos esetekben azonban lehetséges, hogy egy alkalmazás nem rendelkezik a legújabbal. Például ha a házirendek késleltetik a frissítéseket, vagy az Office 365 frissítései a késleltetett csatornán vannak.

## <a name="server-name"></a>Kiszolgálónév

Amikor létrehoz egy Analysis Services kiszolgálót az Azure-ban, meg kell adnia egy egyedi nevet és azt a régiót, ahol a kiszolgálót létre kívánja hozni. Amikor a kiszolgáló nevét adja meg egy kapcsolatban, a kiszolgáló elnevezési sémája a következőket eredményezi:

```
<protocol>://<region>/<servername>
```
 Ahol a protokoll karakterlánc **asazure**, a régió az az URI, ahol a kiszolgáló létrejött (például westus.asazure.Windows.net), a servername pedig a régión belüli egyedi kiszolgáló neve.

### <a name="get-the-server-name"></a>Kiszolgáló nevének lekérése

A **Azure Portal** > Server > **Áttekintés** > **kiszolgáló neve mezőben**másolja a teljes kiszolgálónevet. Ha a szervezet többi felhasználója is csatlakozik ehhez a kiszolgálóhoz, ezt a kiszolgálónevet megoszthatja velük. A kiszolgáló nevének megadásakor a teljes elérési utat kell használni.

![A kiszolgáló nevének lekérése az Azure-ban](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

> [!NOTE]
> Az USA 2. keleti régiójának protokollja a **aspaaseastus2**.

## <a name="connection-string"></a>Kapcsolati sztring

Ha a táblázatos objektummodell használatával csatlakozik Azure Analysis Serviceshoz, használja a következő kapcsolati karakterlánc-formátumokat:

###### <a name="integrated-azure-active-directory-authentication"></a>Integrált Azure Active Directory hitelesítés

Ha elérhető, az integrált hitelesítés felveszi a Azure Active Directory hitelesítő adatokat tartalmazó gyorsítótárat. Ha nem, az Azure bejelentkezési ablak jelenik meg.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```


###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Hitelesítés Azure Active Directory felhasználónévvel és jelszóval

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

###### <a name="windows-authentication-integrated-security"></a>Windows-hitelesítés (beépített biztonság)

Használja az aktuális folyamatot futtató Windows-fiókot.

```
"Provider=MSOLAP;Data Source=<Azure AS instance name>; Integrated Security=SSPI;Persist Security Info=True;"
```

## <a name="connect-using-an-odc-file"></a>Kapcsolat. odc-fájl használatával

Az Excel régebbi verzióival a felhasználók Office-adatkapcsolati (. odc) fájl használatával csatlakozhatnak egy Azure Analysis Services-kiszolgálóhoz. További információt az [Office-Adatkapcsolódási (. odc) fájl létrehozása](analysis-services-odc.md)című témakörben talál.


## <a name="next-steps"></a>Következő lépések

[Kapcsolódjon az Excel](analysis-services-connect-excel.md)    
[Kapcsolódjon a Power BI](analysis-services-connect-pbi.md)   
[A kiszolgáló kezelése](analysis-services-manage.md)   

