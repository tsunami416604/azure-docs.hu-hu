---
title: Helyszíni adatátjáró telepítése
description: Mielőtt az Azure Logic Apps-ből elérne adatokat a helyszínen, töltse le és telepítse a helyszíni adatátjárót
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: 797cd82327d68003d4e5f007d1f16e9534092ac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283991"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Helyszíni adatátjáró telepítése az Azure Logic Appshez

Mielőtt [csatlakozhatna a helyszíni adatforrásokhoz az Azure Logic Apps alkalmazásból,](../logic-apps/logic-apps-gateway-connection.md)töltse le és telepítse a [helyszíni adatátjárót](https://aka.ms/on-premises-data-gateway-installer) egy helyi számítógépre. Az átjáró hídként működik, amely gyors adatátvitelt és titkosítást biztosít a helyszíni adatforrások és a logikai alkalmazások között. Ugyanazt az átjáró-telepítést használhatja más felhőszolgáltatásokkal, például a Power BI-val, a Power Automate-vel, a Power Apps-szel és az Azure Analysis Services-szel. Az átjáró szolgáltatásokkal való használatáról az alábbi cikkekben talál tájékoztatást:

* [A Microsoft Power Automate helyszíni adatátjárója](/power-automate/gateway-reference)
* [Helyszíni Microsoft Power BI adatátjáró](/power-bi/service-gateway-onprem)
* [Helyszíni Microsoft Power Apps-átjáró](/powerapps/maker/canvas-apps/gateway-reference)
* [Helyszíni Azure Analysis Services helyszíni adatátjáró](../analysis-services/analysis-services-gateway.md)

Ez a cikk bemutatja, hogyan töltheti le, telepítheti és állíthatja be a helyszíni adatátjárót, hogy az Azure Logic Apps helyszíni adatforrásai elérhetők vehessenek. Az [adatátjáró működéséről](#gateway-cloud-service) a témakör későbbi részében olvashat bővebben. Az átjáróról további információt a [Mi a helyszíni átjáró?](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)

<a name="requirements"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik előfizetéssel rendelkező Azure-fiókkal, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

  * Az Azure-fióknak egyetlen [Azure Active Directory (Azure AD) bérlőhöz vagy könyvtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology)kell tartoznia. Ugyanazt az Azure-fiókot kell használnia az átjáró helyi számítógépen történő telepítéséhez és felügyeletéhez.

  * Az átjáró telepítése során bejelentkezik az Azure-fiókjával, amely az átjáró telepítését az Azure-fiókhoz és csak az adott fiókhoz kapcsolja. Később az Azure Portalon ugyanazt az Azure-fiókot és az Azure AD-bérlőt kell használnia, amikor olyan Azure-átjáró-erőforrást hoz létre, amely regisztrálja és állítja az átjáró telepítését. Az Azure Logic Apps, helyszíni eseményindítók és műveletek, majd az átjáró erőforrás helyszíni adatforrásokhoz való csatlakozáshoz.

    > [!NOTE]
    > Csak egy átjáró-telepítést és egy Azure-átjáró-erőforrást kapcsolhat egymáshoz. Ugyanazt az átjáró-telepítést nem kapcsolhatja össze több Azure-fiókkal vagy Azure-átjáró-erőforrással. Az Azure-fiók azonban több átjáró-telepítéshez és Azure-átjáró-erőforrásokhoz is kapcsolódhat. Egy helyszíni eseményindító vagy művelet, választhat a különböző Azure-előfizetések, majd válasszon ki egy társított átjáró-erőforrás.

  * Munkahelyi vagy iskolai fiókkal ( más néven *szervezeti* fiókkal) kell bejelentkeznie, amely így néz `username@contoso.com`ki. Az Azure B2B (vendég) fiókok vagy személyes Microsoft-fiókok nem használhatók, például @hotmail.com vagy @outlook.com.

    > [!TIP]
    > Ha office 365-ös ajánlatra iratkozott fel, és nem adta meg `username@domain.onmicrosoft.com`munkahelyi e-mail címét, a címe így nézhet ki: A fiók egy bérlőn belül van tárolva egy Azure Active Directoryban (Azure AD). A legtöbb esetben az Azure AD-fiók egyszerű felhasználóneve (UPN) megegyezik az e-mail-címével.
    >
    > A Microsoft-fiókhoz kapcsolódó [Visual Studio Standard-előfizetés](https://visualstudio.microsoft.com/vs/pricing/) használatához először [hozzon létre egy bérlőt az Azure AD-ben,](../active-directory/develop/quickstart-create-new-tenant.md) vagy használja az alapértelmezett könyvtárat. Adjon hozzá egy jelszót tartalmazó felhasználót a címtárhoz, majd adjon hozzáférést a felhasználónak az Azure-előfizetéséhez. Ezután az átjáró telepítése során ezzel a felhasználónévvel és jelszóval jelentkezhet be.

* A helyi számítógépre vonatkozó követelmények a következők:

  **Minimumkövetelmények**

  * .NET keretrendszer 4.7.2
  * 64 bites Windows 7 vagy Windows Server 2008 R2 (vagy újabb)

  **Ajánlott követelmények**

  * 8 magos CPU
  * 8 GB memória
  * A Windows Server 2012 R2 vagy újabb rendszer 64 bites verziója
  * SSD-tároló a nyomtatási sorhoz

  > [!NOTE]
  > Az átjáró nem támogatja a Windows Server Core rendszert.

* **Kapcsolódó szempontok**

  * Telepítse a helyszíni adatátjárót csak helyi számítógépre, tartományvezérlőre ne telepítse. Az átjárót nem kell ugyanarra a számítógépre telepítenie, mint az adatforrást. Az összes adatforráshoz csak egy átjáróra van szükség, így nem kell minden adatforráshoz telepítenie az átjárót.

    > [!TIP]
    > A késés minimalizálása érdekében telepítheti az átjárót az adatforráshoz a lehető legközelebb, vagy ugyanazon a számítógépen, feltéve, hogy rendelkezik engedélyekkel.

  * Telepítse az átjárót egy vezetékes hálózaton lévő számítógépre, amely csatlakozik az internethez, mindig be van kapcsolva, és nem alszik. Ellenkező esetben az átjáró nem futtatható, és a vezeték nélküli hálózaton a teljesítmény csökkenhet.

  * Ha Windows-hitelesítést kíván használni, telepítse az átjárót olyan számítógépre, amely az adatforrásokkal azonos Active Directory-környezet tagja.

  * Az átjáró telepítéséhez kiválasztott régió ugyanaz a hely, amelyet akkor kell kijelölnie, amikor később létrehozza az Azure átjáró-erőforrást a logikai alkalmazáshoz. Alapértelmezés szerint ez a régió ugyanaz a hely, mint az Azure AD-bérlő, amely kezeli az Azure-fiók. Az átjáró telepítése során azonban módosíthatja a helyet.

  * Ha az átjáró telepítését a legújabb verzióra frissíti, először távolítsa el a jelenlegi átjárót a tisztább élmény érdekében.

  * Az átjáró két móddal rendelkezik: normál és személyes móddal, amely csak a Power BI-ra vonatkozik. Ugyanazon a számítógépen nem futtathat egynél több átjárót ugyanabban a módban.

  * Az Azure Logic Apps támogatja az olvasási és írási műveleteket az átjárón keresztül. Ezek a műveletek azonban [korlátozzák a hasznos teher méretét](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Adatátjáró telepítése

1. [Töltse le és futtassa az átjárótelepítőt a helyi számítógépen.](https://aka.ms/on-premises-data-gateway-installer)

1. Tekintse át a minimális követelményeket, tartsa meg az alapértelmezett telepítési útvonalat, fogadja el a használati feltételeket, majd válassza a **Telepítés**lehetőséget.

   ![A követelmények áttekintése és a használati feltételek elfogadása](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Az átjáró sikeres telepítése után adja meg az Azure-fiókjához szükséges e-mail címet, majd válassza a **Bejelentkezés**lehetőséget például:

   ![Bejelentkezés munkahelyi vagy iskolai fiókkal](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Az átjáró telepítése csak egy Azure-fiókhoz kapcsolódhat.

1. Válassza **az Új átjáró regisztrálása a számítógépen** > Válassza a**Tovább lehetőséget.** Ez a lépés regisztrálja az átjáró telepítését az [átjárófelhő-szolgáltatással.](#gateway-cloud-service)

   ![Átjáró regisztrálása a helyi számítógépen](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Adja meg ezeket az információkat az átjáró telepítéséhez:

   * Az Azure AD-bérlőben egyedülálló átjárónév
   * A helyreállítási kulcs, amelynek legalább nyolc karakterből kell rendelkeznie, és amelyet használni szeretne
   * A helyreállítási kulcs megerősítése

   ![Információ biztosítása az átjáró telepítéséhez](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Mentse és tartsa a helyreállítási kulcsot biztonságos helyen. Szüksége van erre a kulcsra, ha módosítani szeretné a helyet, áthelyezést, helyreállítást vagy átjárótelepítést.

   Vegye figyelembe a **Hozzáadás meglévő átjárófürthöz**lehetőséget, amelyet akkor választhat ki, amikor további átjárókat telepít [a magas rendelkezésre állású helyzetekhez.](#high-availability)

1. Ellenőrizze a régióban az átjáró felhőszolgáltatás és az [Azure Service Bus,](https://azure.microsoft.com/services/service-bus/) amely az átjáró telepítése által használt. Alapértelmezés szerint ez a régió ugyanazon a helyen, mint az Azure AD-bérlő az Azure-fiók.

   ![Régió megerősítése az átjárószolgáltatáshoz és a szolgáltatási buszhoz](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Az alapértelmezett terület elfogadásához válassza a **Konfigurálás**lehetőséget. Ha azonban az alapértelmezett régió nem a legközelebbi, módosíthatja a régiót.

   *Miért érdemes módosítani az átjáró telepítésének régióját?*

   Például a késés csökkentése érdekében módosíthatja az átjáró régióját ugyanabba a régióba, mint a logikai alkalmazás. Vagy előfordulhat, hogy a helyszíni adatforráshoz legközelebb eső régiót választja ki. Az *átjáró-erőforrás az Azure-ban* és a logikai alkalmazás különböző helyekkel rendelkezhet.

   1. Az aktuális terület mellett válassza a **Régió módosítása**lehetőséget.

      ![Az aktuális átjáróterület módosítása](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. A következő lapon nyissa meg a **Terület kijelölése** listát, jelölje ki a kívánt területet, és válassza a **Kész**lehetőséget.

      ![Másik régió kiválasztása az átjárószolgáltatáshoz](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Tekintse át az információkat a végső megerősítési ablakban. Ez a példa ugyanazt a fiókot használja a Logic Apps, a Power BI, a Power Apps és a Power Automate, így az átjáró elérhető az összes ilyen szolgáltatások. Ha készen áll, válassza a **Bezárás**gombot.

   ![Adatátjáró adatainak megerősítése](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Most [hozza létre az Azure-erőforrást az átjáró telepítéséhez.](../logic-apps/logic-apps-gateway-connection.md)

## <a name="check-or-adjust-communication-settings"></a>A kommunikációs beállítások ellenőrzése vagy módosítása

A helyszíni adatátjáró az [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) felhőalapú kapcsolatátantól függ, és létrehozza a megfelelő kimenő kapcsolatokat az átjáró társított Azure-régiójához. Ha a munkakörnyezet megköveteli, hogy a forgalom megy keresztül egy proxy vagy tűzfal az internet eléréséhez, ez a korlátozás megakadályozhatja, hogy a helyszíni adatátjáró csatlakozik az átjáró felhőszolgáltatás és az Azure Service Bus. Az átjáró számos kommunikációs beállítással rendelkezik, amelyek et be lehet állítani. További információt az alábbi témakörökben talál:

* [A helyszíni adatátjáró kommunikációs beállításainak módosítása](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Helyszíni adatátjáró proxybeállításainak konfigurálása](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Magas rendelkezésre állás támogatása

A helyszíni adatelérések egyetlen meghibásodási pontjainak elkerülése érdekében több átjárótelepítést is létrehozhat (csak szabványos módban), és mindegyikkel rendelkezhet egy másik számítógépen, és fürtként vagy csoportként állíthatja be őket. Így, ha az elsődleges átjáró nem érhető el, az adatkérések a második átjáróhoz lesznek irányítva, és így tovább. Mivel a számítógépre csak egy szabványos átjáró telepíthető, a fürtben lévő minden további átjárót egy másik számítógépre kell telepítenie. A helyszíni adatátjáróval dolgozó összes összekötő támogatja a magas rendelkezésre állást.

* Legalább egy átjárótelepítéssel rendelkeznie kell ugyanazzal az Azure-fiókkal, mint az elsődleges átjáró és a telepítés helyreállítási kulcsa.

* Az elsődleges átjárónak 2017 novemberétől vagy újabb verziótól kell futtatnia az átjárófrissítést.

Az elsődleges átjáró beállítása után, amikor egy másik átjáró telepítésére megy, válassza a **Hozzáadás egy meglévő átjárófürthöz**lehetőséget, válassza ki az elsődleges átjárót, amely az első telepített átjáró, és adja meg az átjáró helyreállítási kulcsát. További információ: [Magas rendelkezésre állású fürtök a helyszíni adatátjáróhoz.](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Hely módosítása, áttelepítése, visszaállítása vagy átvehetése a meglévő átjáró felett

Ha módosítania kell az átjáró helyét, át kell helyeznie az átjáró telepítését egy új számítógépre, helyre kell állítania egy sérült átjárót, vagy át kell vennie a tulajdonjogát egy meglévő átjárónak, akkor szüksége van az átjáró telepítése során biztosított helyreállítási kulcsra.

1. Futtassa az átjárótelepítőt azon a számítógépen, amelyen a meglévő átjáró található. Ha nem rendelkezik a legújabb átjárótelepítővel, [töltse le a legújabb átjáróverziót.](https://aka.ms/on-premises-data-gateway-installer)

   > [!NOTE]
   > Mielőtt visszaállítana az átjárót azon a számítógépen, amelyen az eredeti átjáró telepítése található, először el kell távolítania az átjárót azadott számítógépen. Ez a művelet bontja az eredeti átjárót.
   > Ha eltávolít vagy töröl egy átjárófürtöt bármely felhőszolgáltatáshoz, nem állíthatja vissza a fürtöt.

1. A telepítő megnyitása után jelentkezzen be ugyanazzal az Azure-fiókkal, amelyet az átjáró telepítéséhez használt.

1. Válassza **az Áttelepítés, visszaállítás vagy átvétel egy meglévő átjáró** > **t,** például:

   ![Válassza a "Meglévő átjáró áttelepítése, visszaállítása vagy átvétele" lehetőséget.](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Válasszon a rendelkezésre álló fürtök ből és átjárókból, és adja meg a kiválasztott átjáró helyreállítási kulcsát, például:

   ![Válassza ki az átjárót, és adja meg a helyreállítási kulcsot](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. A régió módosításához válassza a **Régió módosítása**lehetőséget, és válassza ki az új régiót.

1. Ha készen áll, válassza a **Konfigurálás** lehetőséget, hogy befejezhesse a feladatot.

## <a name="tenant-level-administration"></a>Bérlőszintű felügyelet

Az Azure AD-bérlők összes helyszíni adatátjárójának láthatóságához a bérlő globális rendszergazdái bejelentkezést végezhetnek a [Power Platform Felügyeleti központjába](https://powerplatform.microsoft.com) bérlői rendszergazdaként, és kiválaszthatják az **Adatátjárók** lehetőséget. További információ: [Bérlőszintű felügyelet a helyszíni adatátjáróhoz.](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Átjáró újraindítása

Alapértelmezés szerint a helyi számítógépen lévő átjárótelepítés "Helyszíni adatátjáró-szolgáltatás" nevű Windows-szolgáltatásfiókként fut. Az átjáró telepítése azonban `NT SERVICE\PBIEgwService` a "Bejelentkezés mint" fiók hitelesítő adatainak nevét használja, és rendelkezik "Bejelentkezés szolgáltatásként" engedélyekkel.

> [!NOTE]
> A Windows szolgáltatásfiók eltér a helyszíni adatforrásokhoz való csatlakozáshoz használt fióktól és a felhőszolgáltatásokba való bejelentkezéskor használt Azure-fióktól.

Mint minden más Windows-szolgáltatás, az átjárót is különböző módokon indíthatja el és állíthatja le. További információt a [Helyszíni adatátjáró újraindítása című témakörben talál.](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart)

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Az átjáró működése

A szervezet felhasználói hozzáférhetnek a helyszíni adatokhoz, amelyekhez már engedélyezett hozzáféréssel rendelkeznek. Mielőtt azonban ezek a felhasználók csatlakozhatnának a helyszíni adatforráshoz, telepítenie kell és be kell állítania egy helyszíni adatátjárót. Általában a rendszergazda az a személy, aki telepíti és beállítja az átjárót. Ezek a műveletek kiszolgálói rendszergazdai engedélyeket vagy a helyszíni kiszolgálókra vonatkozó speciális ismereteket igényelhetnek.

Az átjáró megkönnyíti a gyorsabb és biztonságosabb kulisszák mögötti kommunikációt. Ez a kommunikáció a felhőben lévő felhasználó, az átjárófelhő-szolgáltatás és a helyszíni adatforrás között áramlik. Az átjárófelhő-szolgáltatás titkosítja és tárolja az adatforrás hitelesítő adatait és az átjáró adatait. A szolgáltatás is irányítja a lekérdezéseket és azok eredményeit a felhasználó, az átjáró és a helyszíni adatforrás között.

Az átjáró tűzfalakkal működik, és csak kimenő kapcsolatokat használ. Minden forgalom az átjáróügynök biztonságos kimenő forgalmaként származik. Az átjáró az [Azure Service Buson](../service-bus-messaging/service-bus-messaging-overview.md)keresztül titkosított csatornákon keresztül továbbítja a helyszíni forrásokból származó adatokat. Ez a szolgáltatásbusz létrehoz egy csatornát az átjáró és a hívó szolgáltatás között, de nem tárol adatokat. Az átjárón keresztül áthaladó összes adat titkosítva van.

![Helyszíni adatátjáró architektúrája](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> A felhőszolgáltatástól függően előfordulhat, hogy be kell állítania egy adatforrást az átjáróhoz.

Az alábbi lépések azt ismertetik, hogy mi történik, ha egy helyszíni adatforráshoz csatlakoztatott elemmel kommunikál:

1. A felhőszolgáltatás létrehoz egy lekérdezést, valamint az adatforrás titkosított hitelesítő adatait. A szolgáltatás ezután elküldi a lekérdezést és a hitelesítő adatokat az átjáróvárólistába feldolgozásra.

1. Az átjáró felhőszolgáltatás elemzi a lekérdezést, és leküldi a kérelmet az Azure Service Bus.

1. Az Azure Service Bus elküldi a függőben lévő kérelmeket az átjárónak.

1. Az átjáró lekérdezi a lekérdezést, visszafejti a hitelesítő adatokat, és ezekkel a hitelesítő adatokkal egy vagy több adatforráshoz csatlakozik.

1. Az átjáró elküldi a lekérdezést az adatforrásnak a futtatáshoz.

1. Az eredményeket az adatforrásból visszaküldi az átjárónak, majd az átjárófelhő-szolgáltatásnak. Az átjáró felhőszolgáltatás ezután az eredményeket használja.

### <a name="authentication-to-on-premises-data-sources"></a>Hitelesítés helyszíni adatforrásoknál

A tárolt hitelesítő adatok segítségével csatlakozhat az átjáróról a helyszíni adatforrásokhoz. A felhasználótól függetlenül az átjáró a tárolt hitelesítő adatok at használja a csatlakozáshoz. Előfordulhatnak hitelesítési kivételek bizonyos szolgáltatásokhoz, például a DirectQueryhez és a LiveConnect for Analysis Serviceshez a Power BI-ban.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

A Microsoft felhőszolgáltatásai [az Azure AD](../active-directory/fundamentals/active-directory-whatis.md) használatával hitelesíthetik a felhasználókat. Az Azure AD-bérlő felhasználóneveket és biztonsági csoportokat tartalmaz. A bejelentkezéshez használt e-mail-cím általában megegyezik a fiók egyszerű felhasználónevével (UPN).

### <a name="what-is-my-upn"></a>Mi az upn?

Ha ön nem tartományi rendszergazda, előfordulhat, hogy nem ismeri az upn-t. A fiók hoz az upn `whoami /upn` megkereséséhez futtassa a parancsot a munkaállomásról. Bár az eredmény úgy néz ki, mint egy e-mail cím, az eredmény a helyi tartományi fiók upn-je.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Helyszíni Active Directory szinkronizálása az Azure AD-vel

A helyszíni Active Directory-fiókok és az Azure AD-fiókok upn-fiókjának azonosnak kell lennie. Ezért győződjön meg arról, hogy minden helyszíni Active Directory-fiók megfelel az Azure AD-fióknak. A felhőszolgáltatások csak az Azure AD-n belüli fiókokról tudnak. Így nem kell fiókot hozzáadnia a helyszíni Active Directoryhoz. Ha a fiók nem létezik az Azure AD-ben, nem használhatja ezt a fiókot.

Az alábbiakban az okat a lehetőségeket olvashatja, amelyekkel a helyszíni Active Directory-fiókokat az Azure AD-vel egyeztetheti.

* Manuálisan adja hozzá a fiókokat az Azure AD-hez.

  Hozzon létre egy fiókot az Azure Portalon vagy a Microsoft 365 felügyeleti központban. Győződjön meg arról, hogy a fióknév megegyezik a helyszíni Active Directory-fiók upn-jének.

* Szinkronizálja a helyi fiókokat az Azure AD-bérlővel az Azure Active Directory Connect eszközzel.

  Az Azure AD Connect eszköz lehetőségeket biztosít a címtár-szinkronizálás és a hitelesítés beállításához. Ezek a beállítások közé tartozik a jelszókivonat-szinkronizálás, az átadó hitelesítés és az összevonás. Ha ön nem bérlői rendszergazda vagy helyi tartományi rendszergazda, forduljon a rendszergazdához az Azure AD Connect beállításának lefelvételéhez. Az Azure AD Connect biztosítja, hogy az Azure AD UPN megegyezik a helyi Active Directory UPN. Ez az egyeztetés segít, ha az Analysis Services élő kapcsolatait használja a Power BI vagy az egyszeri bejelentkezési (SSO) funkciókkal.

  > [!NOTE]
  > A fiókok szinkronizálása az Azure AD Connect eszközzel új fiókokat hoz létre az Azure AD-bérlőben.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Gyakori kérdések és hibaelhárítás

További információt az alábbi témakörökben talál:

* [Helyszíni adatátjáró – gyakori kérdések](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [A helyszíni adatátjáró hibaelhárítása](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Átjáró teljesítményének monitorozása és optimalizálása](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>További lépések

* [Csatlakozás helyszíni adatokhoz logikai alkalmazásokból](../logic-apps/logic-apps-gateway-connection.md)
* [Vállalati integrációs funkciók](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Az Azure Logic Apps összekötői](../connectors/apis-list.md)
