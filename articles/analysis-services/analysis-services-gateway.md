---
title: Helyi adatátjáró
description: Egy helyszíni átjáró szükség, ha az Analysis Services-kiszolgálóhoz az Azure-ban a helyszíni adatforrásokhoz csatlakoznak.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f13dd1282a6384a0acca4c6936fe7900a051795f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896023"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Csatlakozás helyszíni adatforrásokhoz a helyszíni adatátjáróval
A helyszíni adatátjáró a helyszíni adatforrások és a felhőben az Azure Analysis Services-kiszolgálók közötti biztonságos adatátvitelt biztosít. Mellett használata több Azure Analysis Services-kiszolgáló ugyanabban a régióban, az átjáró legújabb verzióját is használható az Azure Logic Apps, a Power bi-ban, a Power Apps és a Microsoft Flow. Egyetlen átjáró ugyanabban az előfizetésben és azonos régióban több szolgáltatást is társíthat. 

A telepítő az átjáró első először az egy négyrészes folyamat:

- **Töltse le és futtassa a telepítőt** – Ez a lépés telepíti egy átjárószolgáltatás a szervezet egyik számítógépén. Is jelentkezik be egy olyan fiókkal, az Azure-bA a [bérlő](/previous-versions/azure/azure-services/jj573650(v=azure.100)#BKMK_WhatIsAnAzureADTenant) Azure ad-ben. Az Azure B2B (vendég) fiókok nem támogatottak.

- **Az átjáró regisztrálásához** – ebben a lépésben megad egy nevet és a helyreállítási kulcsra az átjáró, és válassza ki a régiót, regisztrálja az átjárót az átjáró Felhőszolgáltatása. Az átjáró-erőforrás regisztrálni lehet bármelyik régióban, de javasoljuk, hogy azt az Analysis Services-kiszolgáló ugyanabban a régióban kell. 

- **Átjáró erőforrás létrehozása az Azure-ban** – ebben a lépésben az Azure-előfizetésében létrehozott egy átjáró-erőforrást.

- **Csatlakoztassa a kiszolgálókat az átjáró-erőforrás** – Ha egy átjáró-erőforrást az előfizetésében, elkezdheti, a kiszolgálók csatlakozik. Több kiszolgáló- és más erőforrások, amennyiben azok ugyanahhoz az előfizetéshez és azonos régióban kapcsolódhat.

Azonnal használatba, lásd: [telepítése és konfigurálása a helyszíni adatátjáró](analysis-services-gateway-install.md).

## <a name="how-it-works"> </a>Működési elv
Windows-szolgáltatás fut az átjárón a szervezet telepít egy számítógépen **a helyszíni adatátjáró**. A helyi szolgáltatás regisztrálva van az átjáró Felhőszolgáltatása Azure Service Buson keresztül. Ezután hozzon létre egy átjáró-erőforrás Gateway Felhőszolgáltatás esetében az Azure-előfizetés. Az Azure Analysis Services-kiszolgáló majd csatlakozik az átjáró-erőforrás. Modellek a kiszolgálón kell lekérdezéseket és feldolgozási források a helyszíni adatok eléréséhez, ha egy lekérdezést és az adatfolyam is járja, az átjáró-erőforrást, az Azure Service Bus, a helyszíni adatátjáró szolgáltatás és az adatforrásokat. 

![Működés](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Lekérdezések és az adatfolyam:

1. Létrejön egy lekérdezés által a felhőszolgáltatáshoz a helyszíni adatforrás titkosított hitelesítő adatokkal. Ezután zajlik az átjáróhoz tartozó üzenetsorba.
2. Az átjáró felhőszolgáltatása elemzi a lekérdezést, majd leküldi a kérelmet a [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. A helyszíni adatátjáró lekérdezi az Azure Service Bus, a függőben lévő kérelmek.
4. Az átjáró megkapja a lekérdezést, visszafejti a hitelesítő adatokat, és ezekkel az adatokkal az adatforrásokhoz csatlakozik.
5. Az átjáró elküldi a lekérdezést az adatforráshoz a végrehajtáshoz.
6. Az eredmények az adatforrásból kerülnek vissza a az átjárót, majd az alakzatot a felhőszolgáltatás és a kiszolgáló.

## <a name="windows-service-account"> </a>Windows-szolgáltatásfiók
A helyszíni adatátjáró használatára van konfigurálva *NT SERVICE\PBIEgwService* a Windows-szolgáltatás bejelentkezési hitelesítő adataihoz. Alapértelmezés szerint akkor jogosult-e a bejelentkezés szolgáltatásként; a gép, amely telepíti az átjárót a környezetében. Ez a hitelesítő adat nem ugyanazt a fiókot a helyszíni adatforrásokhoz való kapcsolódáshoz használt vagy az Azure-fiókjával.  

Ha problémák merülnek fel a proxykiszolgálón miatt a hitelesítés, előfordulhat, hogy módosítani szeretné a Windows-szolgáltatásfiókot egy tartományi felhasználóra vagy felügyelt szolgáltatásfiókra.

## <a name="ports"> </a>Portok
Az átjáró egy kimenő kapcsolatot, az Azure Service Bus hoz létre. Kimenő portokon kommunikál: A TCP 443 (alapméretezett), 5671, 5672, 9350 – 9354.  Az átjáró nem igényel bejövő portokat.

Javasoljuk, hogy a tűzfal az adatrégió IP-címei engedélyezési listáján. Letöltheti a [a Microsoft Azure Datacenter IP-címlistáját](https://www.microsoft.com/download/details.aspx?id=41653). Ez a lista hetente frissül.

> [!NOTE]
> Az Azure Datacenter IP-listán szereplő IP-címek vannak a CIDR-jelölésrendszerben. További tudnivalókért lásd: [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
>
>

Az alábbiakban a teljes tartománynevek az átjáró által használt.

| Tartománynevek | Kimenő portok | Leírás |
| --- | --- | --- |
| *.powerbi.com |80 |A telepítő letöltéséhez használt HTTP. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queueing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |A Service Bus Relay (a 443-as igényel a hozzáférés-vezérlési token beszerzéséhez) TCP-n keresztül figyelők |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Az internetkapcsolat tesztelésére, ha az átjáró nem érhető el a Power BI szolgáltatás által használt. |
| *.microsoftonline-p.com |443 |Konfigurációjától függően a hitelesítéshez használt. |

### <a name="force-https"></a>Az Azure Service busszal HTTPS-kommunikáció kényszerítése
Kényszerítheti az átjárót, közvetlen TCP helyett HTTPS-en keresztül az Azure Service Bus folytatott kommunikációhoz azonban ez így is nagymértékben csökkenti a teljesítményt. Módosíthatja a *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* fájl értékét `AutoDetect` való `Https`. Ez a fájl általában *C:\Program Files\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="tenant-level-administration"></a>Bérlőszintű felügyelet 

Jelenleg nincs olyan hely, ahol a bérlői rendszergazdák kezelhetik az átjárók, amelyek más felhasználók telepítve és konfigurálva.  Ha Ön Bérlői rendszergazda, javasolt, kérje meg a felhasználókat a szervezetben, hogy vegye fel Önt rendszergazdaként minden átjáró telepítése. Ez lehetővé teszi, hogy az átjáró beállításai panelen vagy keresztül a szervezet összes átjáróját kezelheti [PowerShell-parancsok](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 


## <a name="faq"></a>Gyakori kérdések

### <a name="general"></a>Általános kérdések

**Q**: Kell egy átjárót az adatforrásokat a felhőben, például az Azure SQL Database? <br/>
**A**: Nem. Az átjáró az csak a helyszíni adatforrásokhoz való kapcsolódáshoz szükséges.

**Q**: Az átjáró telepíthető ugyanarra a gépre, az adatforrással rendelkezik? <br/>
**A**: Nem. Az átjáró csak kell csatlakozni a kiszolgálóhoz, általában az ugyanazon a hálózaton lévő képességét.

<a name="why-azure-work-school-account"></a>

**Q**: Miért kell jelentkezzen be munkahelyi vagy iskolai fiók segítségével? <br/>
**A**: Csak használhatja egy szervezeti munkahelyi vagy iskolai fiókot a helyszíni adatátjáró telepítése során. És, hogy fióknak kell lennie az előfizetés ugyanabban a bérlőben, konfigurál az átjáró erőforrás. A bejelentkezési fiók tárolva van egy Azure Active Directory (Azure AD) által felügyelt bérlői. Általában az Azure AD-fiókot egyszerű felhasználónév (UPN) megegyezik az e-mail-cím.

**Q**: Hol tárolódnak a hitelesítő adataim? <br/>
**A**: A megadott hitelesítő adatainak az adatforrások vannak titkosítva, és az átjáró Felhőszolgáltatása tárolja. A hitelesítő adatokat, a helyszíni adatátjáró lesznek visszafejtve.

**Q**: Vannak a hálózati sávszélességgel kapcsolatos követelmények? <br/>
**A**: Ajánlott a hálózati kapcsolat magas átviteli sebességű rendelkezik. Minden környezet más, és küldött adatok mennyisége hatással van az eredményeket. Az ExpressRoute használata segíti a helyszíni és az Azure-adatközpontok között teljesítmény szintjének biztosítása érdekében.
Használhatja a harmadik féltől Azure Speed Test app sebességteszt az átviteli sebességet.

**Q**: Mi az a lekérdezések futtatásának késése az adatforrás és az átjáró? Mi az a legjobb architektúra? <br/>
**A**: Hálózati késés csökkentéséhez telepítse a közel az adatforráshoz az átjárót, amennyire csak lehetséges. Ha az átjárót a tényleges adatforrásra is telepíthető, a közelségi minimalizálja a késést. Érdemes lehet túl az adatközpontokban. Például ha a szolgáltatás az USA nyugati adatközpontjában használ, és már van egy Azure-beli virtuális gépen futó SQL Server, az Azure virtuális Gépen kell lennie az USA nyugati adatközpontjába túl. A közelségi minimalizálja a hálózati késést, és ezzel elkerülheti a kimenő forgalom költségeit, az Azure virtuális gépen.

**Q**: Hogyan lesznek visszaküldve az eredmények a felhőbe? <br/>
**A**: Eredmények küldése az Azure Service Bus keresztül történik.

**Q**: Vannak bemenő kapcsolatok az átjáróba a felhőben? <br/>
**A**: Nem. Az átjáró kimenő kapcsolatokat használ az Azure Service Bus.

**Q**: Mi történik, ha letiltom a kimenő kapcsolatokat? Hogyan készüljek megnyitásához? <br/>
**A**: Tekintse meg a portot és az átjáró által használt gazdagépek.

**Q**: Mi a tényleges Windows-szolgáltatás neve?<br/>
**A**: A szolgáltatások az átjáró neve helyszíni adatátjáró szolgáltatás.

**Q**: Futtatható az átjáró Windows-szolgáltatás Azure Active Directory-fiókkal? <br/>
**A**: Nem. A Windows-szolgáltatás egy érvényes Windows-fiókkal kell rendelkeznie. Alapértelmezés szerint a szolgáltatás fut a szolgáltatás biztonsági NT SERVICE\PBIEgwService.

**Q**: Hogyan hajthatom végre átvétel az átjáró? <br/>
**A**: Az átjáró átvétele (telepítés/módosítás futtatja a Vezérlőpult > programok), kell lennie az Azure-ban az átjáró erőforrás tulajdonosa, és a helyreállítási kulcsot. Erőforrás-tulajdonosok átjáró is konfigurálható, a hozzáférés-vezérlés.

### <a name="high-availability"></a>Magas rendelkezésre állás és vészhelyreállítás helyreállítási

**Q**: Hogyan lehet magas rendelkezésre állású van?  
**A**: Egy átjárót telepítheti egy fürt létrehozása egy másik számítógépen. További tudnivalókért lásd: [magas rendelkezésre állású fürtök helyszíni adatátjáróhoz tartozó](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters) a Power BI Gateway dokumentumokhoz.

**Q**: Milyen lehetőségek állnak rendelkezésre a vész-helyreállítási? <br/>
**A**: A helyreállítási kulcs használatával egy átjáró visszaállítását vagy áthelyezését. Az átjáró telepítésekor adja meg a helyreállítási kulcsot.

**Q**: Mi az az előnye, hogy a helyreállítási kulcs? <br/>
**A**: A helyreállítási kulcs áttelepítéséhez vagy helyreállíthatja átjárója beállításait egy vészhelyzetet követően lehetővé teszi.

## <a name="troubleshooting"> </a>Hibaelhárítás

**Q**: Miért nem látom az átjárópéldányok listájának és a saját átjáró az átjáró-erőforrás létrehozásakor az Azure-ban? <br/>
**A**: Két lehetséges oka van. Először is az átjáró az aktuális vagy valamilyen más előfizetésben már létrehozott egy erőforrást. Ezt a lehetőséget, hogy a típusú erőforrások számbavétele **a helyszíni Adatátjárók** a portálról. Mindenképp válassza ki az összes előfizetés összes erőforrását számbavétele során. Az erőforrás létrehozása után az átjáró nem jelenik meg az átjáró-erőforrás létrehozása portáljának felületén az átjárópéldányok listájának. A második lehetőség az, hogy az átjáró telepítve van a felhasználó Azure AD-identitásnak eltér a felhasználó bejelentkezett az Azure Portalra. Oldja meg, jelentkezzen be az átjáró telepítve van a felhasználó ugyanazt a fiókot a portálra.

**Q**: Hogyan tekinthetem meg mit lekérdezés folyamatban van a helyszíni adatforrás küldött? <br/>
**A**: Engedélyezheti a lekérdezések nyomon követését, amely tartalmazza a lekérdezéseket küldött el. Ne felejtse el módosítani a lekérdezés nyomkövetés vissza az eredeti értékre, a hibaelhárítás végrehajtása után. Hagyja a lekérdezések nyomon követését bekapcsolva hoz létre a nagyobb naplókat.

Tekintse meg az adatforrást a lekérdezések nyomon követésére rendelkező eszközöket is. Például használhatja Extended Eventset vagy az SQL Profiler az SQL Server és az Analysis Services.

**Q**: Hol találhatók az átjárónaplók? <br/>
**A**: Tekintse meg a naplókat a cikk későbbi részében.

### <a name="update"></a>A legújabb verzióra frissíteni

Számos probléma akkor merülhet fel, ha az átjáró verziója elavult válik. Általános gyakorlat győződjön meg arról, hogy a legújabb verzióját használja. Ha az átjáró egy hónapig vagy hosszabb ideig nem frissítette, előfordulhat, hogy fontolja meg az átjáró legújabb verziójának telepítését, és tekintse meg, ha Reprodukálja a problémát.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Hiba: Nem sikerült hozzáadni a felhasználói csoporthoz. (-2147463168 PBIEgwService Teljesítménynapló felhasználói)

Előfordulhat, hogy megjelenik a hibaüzenet, ha az átjáró telepítésekor a tartományvezérlőn, amely nem támogatott. Győződjön meg arról, hogy telepít-e az átjárót olyan gépre, amely nem tartományvezérlő.

## <a name="logs"></a>Naplók

Naplófájlok fontos tényezőként hibaelhárítása során.

#### <a name="enterprise-gateway-service-logs"></a>Vállalati átjárószolgáltatás naplói

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Konfigurációs naplók

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`


#### <a name="event-logs"></a>Eseménynaplók

Az adatkezelési átjáró, a PowerBIGateway naplókat alatt található **alkalmazás- és szolgáltatásnaplók**.


## <a name="telemetry"></a>Telemetria
Telemetria használható figyeléséhez és hibaelhárításához. Alapértelmezés szerint

**Telemetria bekapcsolása**

1.  Ellenőrizze a On-premises data gateway ügyfélkönyvtár azon a számítógépen. Jellemzően a **%systemdrive%\Program Files\On-premises data gateway**. Vagy nyissa meg a szolgáltatások konzolt, és ellenőrizze a végrehajtható fájl elérési útja: A helyszíni adatátjáró szolgáltatás tulajdonsága.
2.  Az ügyfél címtárból Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config fájl. Módosítsa a SendTelemetry beállítást igaz értékre.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  A módosítások mentéséhez és a Windows-szolgáltatást: A helyszíni adatátjáró szolgáltatás.




## <a name="next-steps"></a>További lépések
* [Telepítse és konfigurálja a helyszíni adatátjáró](analysis-services-gateway-install.md).   
* [Analysis Services kezelése](analysis-services-manage.md)
* [Adatok lekérése az Azure Analysis Services](analysis-services-connect.md)
