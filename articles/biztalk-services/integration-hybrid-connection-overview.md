---
title: "A hibrid kapcsolatok áttekintése | Microsoft Docs"
description: "Ez a cikk a hibrid kapcsolatokat, a biztonságot, a TCP-portokat és a támogatott konfigurációkat ismerteti. MABS, WABS."
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: 216e4927-6863-46e7-aa7c-77fec575c8a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/18/2016
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 9367d6f57e694c8a438781004ef29a09de77aaa8
ms.contentlocale: hu-hu
ms.lasthandoff: 06/03/2017


---
# <a name="hybrid-connections-overview"></a>Hibrid kapcsolatok áttekintése

> [!IMPORTANT]
> A BizTalk Hybrid Connections ki van vezetve, helyét az App Service Hybrid Connections vette át. További tájékoztatást egyebek között a meglévő BizTalk Hybrid Connections-kapcsolatok kezeléséről az [Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) című cikkben talál.

A hibrid kapcsolatok bemutatása a támogatott konfigurációkat és a szükséges TCP-portokat sorolja fel.

## <a name="what-is-a-hybrid-connection"></a>Mi a hibrid kapcsolat
A hibrid kapcsolatok az Azure BizTalk Services egyik funkciója. A hibrid kapcsolatokkal könnyen és kényelmesen csatlakozhatja az Azure App Service Web Apps (korábban Websites) szolgáltatását és az Azure App Service Mobile Apps (korábban Mobile Services) szolgáltatását a tűzfal mögött található helyszíni erőforrásokhoz.

![Hibrid kapcsolatok][HCImage]

A hibrid kapcsolatok előnyei például a következők:

* A Web Apps és a Mobile Apps biztonságosan érheti el a meglévő helyszíni adatokat és szolgáltatásokat.
* Több Web Apps- vagy Mobile Apps-alkalmazás osztozhat egy hibrid kapcsolaton egy helyszíni erőforrás eléréséhez.
* Minimális számú TCP-portra van szükség a hálózat eléréséhez.
* A hibrid kapcsolatokat használó alkalmazások csak a hibrid kapcsolaton keresztül közzétett adott helyszíni erőforrást érik el.
* Bármely, statikus TCP-portot használó helyszíni erőforráshoz, például SQL Serverhez, MySQL-hez, HTTP Web API-khoz, mobilszolgáltatásokhoz és a legtöbb egyéni webszolgáltatáshoz csatlakozhat.
  
  > [!NOTE]
  > A dinamikus portokat használó TCP-alapú szolgáltatások (például az FTP passzív mód vagy a kiterjesztett paszív mód) jelenleg nem támogatottak. Az LDAP sem támogatott. Az LDAP statikus TCP-portot használ, de UDP-t is használhat. Ennek következtében nem támogatott.
  > 
  > 
* A Web Apps (.NET, PHP, Java, Python, Node.js) és a Mobile Apps (Node.js, .NET) által támogatott összes keretrendszerrel használható.
* A Web Apps és a Mobile Apps pontosan ugyanúgy éri el a helyszíni erőforrásokat, mintha a web- vagy mobilalkalmazás a helyi hálózaton lenne. A helyszínen használt kapcsolati karakterlánc például az Azure-ban is használható.

A hibrid kapcsolatokkal a vállalati rendszergazdáknak vezérelhetik és elemezhetik is a hibrid alkalmazások által elért vállalati erőforrásokat, beleértve a következőket:

* A rendszergazdák csoportházirend-beállításokkal engedélyezhetik a hibrid kapcsolatokat a hálózaton, és a hibrid alkalmazások által elérhető erőforrásokat jelölhetnek ki.
* A vállalati hálózaton az esemény- és vizsgálati naplók nyújtanak betekintést a hibrid kapcsolatok által elért erőforrásokba.

## <a name="example-scenarios"></a>Példaforgatókönyvek
A hibrid kapcsolatok a keretrendszerek és alkalmazások következő kombinációit támogatják:

* .NET-keretrendszer hozzáférése az SQL Serverhez
* .NET-keretrendszer hozzáférése HTTP/HTTPS-szolgáltatásokhoz WebClienttel
* PHP hozzáférése az SQL Serverhez, MySQL-hez
* Java hozzáférése az SQL Serverhez, MySQL-hez és Oracle-höz
* Java hozzáférése HTTP/HTTPS-szolgáltatásokhoz

Amikor hibrid kapcsolatokkal éri el a helyszíni SQL Servert, vegye figyelembe a következőket:

* Az SQL Express elnevezett példányait statikus portok használatára kell konfigurálni. Alapértelmezés szerint az SQL Express elnevezett példányai dinamikus portokat használnak.
* Az SQL Express alapértelmezett példányai statikus portot használnak, de engedélyezni kell a TCP protokollt. Alapértelmezés szerint a TCP nem engedélyezett.
* Fürtszolgáltatási vagy rendelkezésre állási csoportok használatakor a `MultiSubnetFailover=true` mód jelenleg nem támogatott.
* Az `ApplicationIntent=ReadOnly` jelenleg nem támogatott.
* SQL-hitelesítésre lehet szükség az Azure-alkalmazás és a helyszíni SQL-kiszolgáló által támogatott végpontok közötti hitelesítési módszerként.

## <a name="security-and-ports"></a>Biztonság és portok
A hibrid kapcsolatok közös hozzáférésű jogosultságkódon (SAS) alapuló hitelesítést használnak az Azure-alkalmazások és a helyszíni hibridkapcsolat-kezelő és a hibrid kapcsolatok közötti kapcsolatok védelméhez. A rendszer külön kapcsolati kulcsokat hoz létre az alkalmazás és a helyszíni hibridkapcsolat-kezelő számára. Ezek a kapcsolati kulcsok egymástól függetlenül frissíthetők és visszavonhatók.

A hibrid kapcsolatok a kulcsok zökkenőmentes és biztonságos elosztását biztosítják az alkalmazások és a helyszíni hibridkapcsolat-kezelő számára.

Lásd: [Create and Manage Hybrid Connections](integration-hybrid-connection-create-manage.md) (Hibrid kapcsolatok létrehozása és felügyelete).

*Az alkalmazások hitelesítése a hibrid kapcsolatoktól elkülönítve történik*. Bármilyen megfelelő hitelesítési módszer használható. A hitelesítési módszer az Azure-felhőben és a helyszíni összetevőkben támogatott végpontok közötti hitelesítési módszerektől függ. Az Azure-alkalmazás például egy helyszíni SQL Servert ér el. Ebben a forgatókönyvben az SQL-hitelesítés lehet a végpontok között támogatott hitelesítési módszer.

#### <a name="tcp-ports"></a>TCP-portok
A hibrid kapcsolatokhoz csak kimenő TCP- vagy HTTP-kapcsolatra van szükség a magánhálózaton. Nem kell tűzfalportokat nyitnia, és nem kell módosítania a hálózatperem konfigurációját ahhoz, hogy bemenő kapcsolatot engedélyezzen a hálózat felé.

A hibrid kapcsolatok a következő TCP-portokat használják:

| Port | Miért szükséges |
| --- | --- |
| 9350 - 9354 |Ezek a portok adatátvitelre szolgálnak. A Service Bus Relay kezelője teszteli a 9350-es portot a TCP-kapcsolat rendelkezésre állásának meghatározásához. Ha elérhető, akkor feltételezi, hogy a 9352-es port is elérhető. Az adatforgalom a 9352-es porton halad át. <br/><br/>Engedélyezze a kimenő kapcsolatokat ezeken a portokon. |
| 5671 |Amikor a 9352-es portot használja az adatforgalomhoz, az 5671-es port szolgál vezérlőcsatornaként. <br/><br/>Engedélyezze a kimenő kapcsolatokat ezen a porton. |
| 80, 443 |Ezek a portok szolgálnak az Azure felé irányuló egyes adatkérések kezdeményezésére. Ezenkívül, ha a 9352-es és az 5671-es port nem használható, *akkor* a 80-as és a 443-as port az adatátvitelhez és a vezérlőcsatornához használt tartalékport.<br/><br/>Engedélyezze a kimenő kapcsolatokat ezeken a portokon. <br/><br/>**Megjegyezés**: nem ajánlott ezeket tartalékportokként használni más TCP-portok helyett. Adatcsatornák esetén a natív TCP helyett a HTTP/WebSocket protokoll használható. Ez kisebb teljesítményt eredményezhet. |

## <a name="next-steps"></a>Következő lépések
[Create and Manage Hybrid Connections (Hibrid kapcsolatok létrehozása és felügyelete)](integration-hybrid-connection-create-manage.md)<br/>
[Azure Web Apps csatlakoztatása helyszíni erőforráshoz](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[Csatlakozás helyszíni SQL Serverhez Azure-webalkalmazásból](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>

## <a name="see-also"></a>Lásd még:
[REST API a BizTalk Services felügyeletéhez a Microsoft Azure-ban](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[BizTalk Services: Kiadások diagramja](biztalk-editions-feature-chart.md)<br/>
[BizTalk-szolgáltatás létrehozása az Azure Portallal](biztalk-provision-services.md)<br/>
[BizTalk Services: Irányítópult, Figyelés és Méret lapok](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

