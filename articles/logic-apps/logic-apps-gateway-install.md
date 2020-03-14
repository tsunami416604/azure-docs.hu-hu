---
title: Helyszíni adatátjáró telepítése
description: Mielőtt a helyszíni adatokhoz hozzáférhessen Azure Logic Appsről, töltse le és telepítse a helyszíni adatátjárót
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: 797cd82327d68003d4e5f007d1f16e9534092ac0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79283991"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Helyszíni adatátjáró telepítése Azure Logic Apps

A helyszíni [adatforrásokhoz való kapcsolódáshoz Azure Logic apps](../logic-apps/logic-apps-gateway-connection.md)a helyi számítógépen töltse le és telepítse a helyszíni [adatátjárót](https://aka.ms/on-premises-data-gateway-installer) . Az átjáró hídként működik, amely gyors adatátvitelt és titkosítást biztosít a helyszínen és a logikai alkalmazásokban lévő adatforrások között. Ugyanezt az átjárót más felhőalapú szolgáltatásokkal is használhatja, mint például a Power BI, az automatizálás, a Power apps és a Azure Analysis Services. Az átjáró ezen szolgáltatásokkal való használatáról a következő cikkekben talál további információt:

* [A Microsoft Power automatizálja a helyszíni adatátjárót](/power-automate/gateway-reference)
* [Microsoft Power BI helyszíni adatátjáró](/power-bi/service-gateway-onprem)
* [Microsoft Power apps helyszíni adatátjáró](/powerapps/maker/canvas-apps/gateway-reference)
* [Helyszíni adatátjáró Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Ez a cikk bemutatja, hogyan töltheti le, telepítheti és állíthatja be a helyszíni adatátjárót, hogy a helyszíni adatforrások hozzáférhessenek Azure Logic Apps. További információ arról, [hogy az adatátjáró hogyan működik a](#gateway-cloud-service) témakör későbbi részében. Az átjáróval kapcsolatos további információkért lásd: [Mi az a helyszíni átjáró](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)?

<a name="requirements"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik előfizetéssel rendelkező Azure-fiókkal, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

  * Az Azure-fióknak egyetlen [Azure Active Directory (Azure ad) bérlőhöz vagy címtárhoz](../active-directory/fundamentals/active-directory-whatis.md#terminology)kell tartoznia. Az átjáró helyi számítógépen való telepítéséhez és felügyeletéhez ugyanazt az Azure-fiókot kell használnia.

  * Az átjáró telepítése során jelentkezzen be az Azure-fiókjával, amely összekapcsolja az átjáró telepítését az Azure-fiókjával, és csak ezt a fiókot. Később, az Azure Portalban ugyanazt az Azure-fiókot és Azure AD-bérlőt kell használnia, amikor olyan Azure Gateway-erőforrást hoz létre, amely regisztrálja és állítja be az átjáró telepítését. Azure Logic Apps a helyszíni eseményindítók és műveletek a helyszíni adatforrásokhoz való kapcsolódáshoz az átjáró-erőforrást használják.

    > [!NOTE]
    > Egy átjárót és egy Azure Gateway-erőforrást is összekapcsolhat egymással. Ugyanahhoz az átjáróhoz tartozó telepítést nem lehet több Azure-fiókhoz vagy Azure Gateway-erőforráshoz kapcsolni. Az Azure-fiókok azonban több átjáró-telepítéshez és Azure Gateway-erőforrásokhoz is csatolhatók. Helyszíni trigger vagy művelet esetén választhat a különböző Azure-előfizetések közül, majd kiválaszthat egy társított átjáró-erőforrást.

  * Be kell jelentkeznie egy munkahelyi fiókkal vagy iskolai fiókkal, más néven *szervezeti* fiókkal, amely a következőhöz hasonlóan néz ki: `username@contoso.com`. Nem használhat Azure B2B-(vendég-) fiókokat vagy személyes Microsoft-fiókokat, például @hotmail.com vagy @outlook.com.

    > [!TIP]
    > Ha regisztrált az Office 365-ajánlatra, és nem adta meg a munkahelyi e-mail-címét, akkor előfordulhat, hogy a cím a következőhöz hasonlóan néz ki: `username@domain.onmicrosoft.com`. A fiókját egy Azure Active Directory (Azure AD) bérlőn belül tárolja a rendszer. A legtöbb esetben az Azure AD-fiókhoz tartozó egyszerű felhasználónév (UPN) megegyezik az e-mail-címével.
    >
    > Ha egy Microsoft-fiókhoz csatolt [Visual Studio standard-előfizetést](https://visualstudio.microsoft.com/vs/pricing/) szeretne használni, először [hozzon létre egy bérlőt az Azure ad-ben](../active-directory/develop/quickstart-create-new-tenant.md) , vagy használja az alapértelmezett könyvtárat. Adjon hozzá egy jelszót tartalmazó felhasználót a címtárhoz, majd adja meg a felhasználónak az Azure-előfizetését. Ezt a felhasználónevet és jelszót használva az átjáró telepítése közben is bejelentkezhet.

* A helyi számítógépekre vonatkozó követelmények:

  **Minimális követelmények**

  * .NET-keretrendszer 4.7.2
  * a Windows 7 vagy a Windows Server 2008 R2 64 bites verziója (vagy újabb)

  **Ajánlott követelmények**

  * 8 magos processzor
  * 8 GB memória
  * a Windows Server 2012 R2 vagy újabb 64 bites verziója
  * SSD-tároló a várólista-tároláshoz

  > [!NOTE]
  > Az átjáró nem támogatja a Windows Server Core-ot.

* **Kapcsolódó megfontolások**

  * Telepítse a helyszíni adatátjárót csak helyi számítógépre, nem tartományvezérlőre. Nem kell telepítenie az átjárót ugyanarra a számítógépre, mint az adatforrást. Az összes adatforráshoz csak egy átjáró szükséges, így nem kell telepítenie az átjárót az egyes adatforrásokhoz.

    > [!TIP]
    > A késés csökkentése érdekében az átjárót a lehető legközelebb az adatforráshoz vagy ugyanazon a számítógépen telepítheti, feltéve, hogy rendelkezik a megfelelő engedélyekkel.

  * Telepítse az átjárót olyan számítógépre, amely vezetékes hálózaton található, az internethez csatlakozik, mindig be van kapcsolva, és nem alvó állapotba kerül. Ellenkező esetben az átjáró nem futtatható, és a teljesítmény a vezeték nélküli hálózaton keresztül csökkenhet.

  * Windows-hitelesítés használata esetén győződjön meg arról, hogy az átjárót olyan számítógépre telepíti, amely az adatforrásokkal megegyező Active Directory-környezet tagja.

  * Az átjáró telepítéséhez kiválasztott régió ugyanaz a hely, amelyet később a logikai alkalmazáshoz tartozó Azure Gateway-erőforrás létrehozásakor ki kell választania. Alapértelmezés szerint ez a régió megegyezik az Azure-fiókját kezelő Azure AD-Bérlővel. A helyet azonban megváltoztathatja az átjáró telepítése közben is.

  * Ha az átjáró telepítését a legújabb verzióra frissíti, először távolítsa el az aktuális átjárót a tisztább élmény érdekében.

  * Az átjárónak két módja van: a standard mód és a személyes mód, amely csak a Power BIre vonatkozik. Ugyanazon a számítógépen nem lehet egynél több olyan átjáró, amely ugyanazon a módban fut.

  * Azure Logic Apps támogatja az olvasási és írási műveleteket az átjárón keresztül. Ezek a műveletek azonban [korlátokkal rendelkeznek a hasznos adatok méretétől függően](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Adatátjáró telepítése

1. [Töltse le és futtassa az átjáró telepítőjét egy helyi számítógépen](https://aka.ms/on-premises-data-gateway-installer).

1. Tekintse át a minimális követelményeket, tartsa meg az alapértelmezett telepítési útvonalat, fogadja el a használati feltételeket, majd válassza a **telepítés**lehetőséget.

   ![A követelmények áttekintése és a használati feltételek elfogadása](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Az átjáró sikeres telepítése után adja meg az Azure-fiókhoz tartozó e-mail-címet, majd válassza a **Bejelentkezés**lehetőséget, például:

   ![Bejelentkezés munkahelyi vagy iskolai fiókkal](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Az átjáró telepítése csak egy Azure-fiókra hivatkozhat.

1. Válassza **az új átjáró regisztrálása ezen a számítógépen > a** **tovább**lehetőséget. Ez a lépés regisztrálja az átjáró telepítését az [átjáró Cloud Service](#gateway-cloud-service)-ben.

   ![Átjáró regisztrálása helyi számítógépen](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Adja meg az átjáró telepítéséhez szükséges információkat:

   * Egy átjáró neve, amely egyedi az Azure AD-bérlőn belül
   * A használni kívánt helyreállítási kulcsnak legalább nyolc karakterből kell állnia.
   * A helyreállítási kulcs megerősítése

   ![Adja meg az átjáró telepítésének adatait](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Mentse és őrizze meg a helyreállítási kulcsot biztonságos helyen. Erre a kulcsra akkor van szükség, ha módosítani szeretné az átjárók helyét, áthelyezését, helyreállítását vagy átvételét.

   Vegye figyelembe, hogy **egy meglévő átjáró fürthöz való hozzáadás**lehetősége, amelyet akkor kell kiválasztani, amikor további átjárókat telepít a [magas rendelkezésre állású forgatókönyvekhez](#high-availability).

1. Keresse meg az átjáró felhőalapú szolgáltatásának régióját, és [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) , amelyet az átjáró telepítése használ. Alapértelmezés szerint ez a régió ugyanaz a hely, mint az Azure AD-bérlő az Azure-fiókjához.

   ![Az átjáró szolgáltatás és a Service Bus régiójának megerősítése](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Az alapértelmezett régió elfogadásához válassza a **Konfigurálás**lehetőséget. Ha azonban az alapértelmezett régió nem az Önhöz legközelebb eső, akkor módosíthatja a régiót.

   *Miért érdemes módosítani a régiót az átjáró telepítésekor?*

   Például a késés csökkentése érdekében előfordulhat, hogy az átjáró régióját a logikai alkalmazással megegyező régióra változtatja. Vagy a helyszíni adatforráshoz legközelebb eső régiót is kiválaszthatja. Az *Azure* -beli átjáró-erőforrás és a logikai alkalmazás különböző helyekkel rendelkezhet.

   1. Az aktuális régió mellett válassza a **régió módosítása**lehetőséget.

      ![Az aktuális átjáró régiójának módosítása](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. A következő lapon nyissa meg a **régió kiválasztása** listát, válassza ki a kívánt régiót, majd válassza a **kész**lehetőséget.

      ![Válasszon másik régiót az átjáró szolgáltatáshoz](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Tekintse át a végső megerősítési ablakban található információkat. Ez a példa ugyanazt a fiókot használja a Logic Apps, a Power BI, a Power apps és a Power automatizáláshoz, így az átjáró elérhető az összes szolgáltatáshoz. Ha elkészült, válassza a **Bezárás**lehetőséget.

   ![Az adatátjáró adatainak megerősítése](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Most [hozza létre az Azure-erőforrást az átjáró telepítéséhez](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>A kommunikációs beállítások keresése vagy módosítása

A helyszíni adatátjáró a felhőalapú kapcsolat [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) függ, és létrehozza a megfelelő kimenő kapcsolatokat az átjáróhoz tartozó Azure-régióhoz. Ha a munkahelyi környezet megköveteli, hogy a forgalom egy proxyn vagy tűzfalon keresztül hozzáférjen az internethez, akkor ez a korlátozás megakadályozhatja, hogy a helyszíni adatátjáró csatlakozzon az átjáró Cloud Service-hez, és Azure Service Bus. Az átjáró számos kommunikációs beállítással rendelkezik, amelyeket módosíthat. További információkért tekintse meg a következő témaköröket:

* [A helyszíni adatátjáró kommunikációs beállításainak módosítása](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Proxybeállítások konfigurálása a helyszíni adatátjáróhoz](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Magas rendelkezésre állás támogatása

Ha el szeretné kerülni a helyszíni adathozzáféréshez szükséges egyetlen meghibásodási pontot, a különböző számítógépeken egyszerre több átjáró telepíthető (csak standard módban), és beállíthatja őket fürtként vagy csoportként. Így ha az elsődleges átjáró nem érhető el, az adatkérések átirányítva a második átjáróra, és így tovább. Mivel a számítógépen csak egy standard átjáró telepíthető, a fürtben lévő minden további átjárót telepíteni kell egy másik számítógépen. A helyszíni adatátjáróval működő összes összekötő támogatja a magas rendelkezésre állást.

* Rendelkeznie kell legalább egy olyan átjáró-telepítéssel, amely ugyanazzal az Azure-fiókkal rendelkezik, mint az elsődleges átjáró, valamint az adott telepítés helyreállítási kulcsa.

* Az elsődleges átjárónak az átjáró frissítését november 2017 vagy újabb verzióra kell futtatnia.

Miután beállította az elsődleges átjárót, amikor egy másik átjárót telepít, válassza a **Hozzáadás meglévő átjáró-fürthöz**lehetőséget, válassza ki az elsődleges átjárót, amely az első telepített átjáró, és adja meg az adott átjáró helyreállítási kulcsát. További információ: [magas rendelkezésre állású fürtök helyszíni adatátjáróhoz](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Megváltoztathatja a meglévő átjáró helyét, áttelepítését, visszaállítását vagy átvételét

Ha módosítania kell az átjáró helyét, helyezze át az átjáró telepítését egy új számítógépre, állítson helyre egy sérült átjárót, vagy vegyen fel egy meglévő átjáró tulajdonjogát, szüksége lesz az átjáró telepítésekor megadott helyreállítási kulcsra.

1. Futtassa az átjáró telepítőjét azon a számítógépen, amelyen a meglévő átjáró található. Ha nem rendelkezik a legújabb átjáró-telepítővel, [töltse le az átjáró legújabb verzióját](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Mielőtt visszaállítja az átjárót azon a számítógépen, amelyen az eredeti átjáró telepítve van, először el kell távolítania az átjárót az adott számítógépen. Ez a művelet leválasztja az eredeti átjárót.
   > Ha bármely felhőalapú szolgáltatáshoz eltávolít vagy töröl egy átjáró-fürtöt, akkor a fürt nem állítható vissza.

1. A telepítő megnyitása után jelentkezzen be ugyanazzal az Azure-fiókkal, amelyet az átjáró telepítéséhez használt.

1. Válassza a **meglévő átjáró áttelepíteni, visszaállítása vagy átvétele** > a **következő**lehetőséget, például:

   ![Válassza a "meglévő átjáró migrálása, visszaállítása vagy átvétele" lehetőséget.](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Válasszon a rendelkezésre álló fürtök és átjárók közül, és adja meg a kiválasztott átjáró helyreállítási kulcsát, például:

   ![Átjáró kiválasztása és helyreállítási kulcs megadása](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. A régió módosításához válassza a **régió módosítása**lehetőséget, és válassza ki az új régiót.

1. Ha elkészült, válassza a **Konfigurálás** lehetőséget a feladat befejezéséhez.

## <a name="tenant-level-administration"></a>Bérlői szintű felügyelet

Az Azure AD-bérlőben található összes helyszíni adatátjáró megismeréséhez a bérlő globális rendszergazdái bejelentkezhetnek a [Power platform felügyeleti központjába](https://powerplatform.microsoft.com) bérlői rendszergazdaként, és az **adatátjárók** lehetőségre is kiválaszthatók. További információ: [bérlői szintű felügyelet a helyszíni adatátjáróhoz](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Átjáró újraindítása

Alapértelmezés szerint az átjáró helyi számítógépen történő telepítése a "helyszíni adatátjáró szolgáltatás" nevű Windows-szolgáltatásfiókként fut. Az átjáró telepítése azonban a "Bejelentkezés mint" fiók hitelesítő adatainak `NT SERVICE\PBIEgwService` nevét használja, és "Bejelentkezés szolgáltatásként" jogosultságokkal rendelkezik.

> [!NOTE]
> A Windows-szolgáltatásfiók eltér a helyszíni adatforrásokhoz való csatlakozáshoz használt fióktól és az Azure-fióktól, amelyet a Cloud Services szolgáltatásba való bejelentkezéskor használ.

A többi Windows-szolgáltatáshoz hasonlóan a különböző módokon is elindíthatja és leállíthatja az átjárót. További információ: [helyszíni adatátjáró újraindítása](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Az átjáró működése

A szervezet felhasználói hozzáférhetnek a helyszíni adataihoz, amelyekhez már engedélyezve van a hozzáférésük. Azonban ahhoz, hogy ezek a felhasználók csatlakozni tudjanak a helyszíni adatforráshoz, telepítenie és be kell állítania egy helyszíni adatátjárót. A rendszergazda általában az a személy, aki telepíti és beállítja az átjárót. A műveletekhez szükség lehet a kiszolgáló-rendszergazdai engedélyekre vagy a helyszíni kiszolgálókkal kapcsolatos speciális ismeretekre.

Az átjáró segíti a gyorsabb és biztonságosabb kommunikációt a színfalak mögött. Ez a kommunikáció a felhőben, az átjáró Cloud Service-ben és a helyszíni adatforrásban lévő felhasználó között áramlik. Az átjáró Cloud Service titkosítja és tárolja az adatforrás hitelesítő adatait és az átjáró részleteit. A szolgáltatás a felhasználók, az átjáró és a helyszíni adatforrás közötti lekérdezéseket és azok eredményeit is átirányítja.

Az átjáró tűzfalakkal működik, és csak kimenő kapcsolatokat használ. Az összes forgalom biztonságos kimenő forgalomnak minősül az átjáró ügynöktől. Az átjáró a helyszíni forrásokból származó adatok továbbítása a [Azure Service Buson](../service-bus-messaging/service-bus-messaging-overview.md)keresztül titkosított csatornákon keresztül történik. Ez a Service Bus létrehoz egy csatornát az átjáró és a hívó szolgáltatás között, de nem tárol semmilyen adattárolót. Az átjárón keresztül áthaladó összes adatátvitel titkosítva van.

![Helyszíni adatátjáró architektúrája](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> A felhőalapú szolgáltatástól függően előfordulhat, hogy be kell állítania egy adatforrást az átjáróhoz.

Ezek a lépések azt írják le, hogy mi történik, ha egy helyszíni adatforráshoz kapcsolódó elemmel működik együtt:

1. A Cloud Service létrehoz egy lekérdezést az adatforrás titkosított hitelesítő adataival együtt. A szolgáltatás ezután elküldi a lekérdezést és a hitelesítő adatokat az átjáró várólistájának feldolgozásra.

1. Az átjáró Cloud Service elemzi a lekérdezést, és leküldi a kérést Azure Service Bus.

1. Azure Service Bus elküldi a függőben lévő kéréseket az átjárónak.

1. Az átjáró lekéri a lekérdezést, visszafejti a hitelesítő adatokat, és egy vagy több adatforráshoz csatlakozik ezekkel a hitelesítő adatokkal.

1. Az átjáró elküldi a lekérdezést az adatforrásnak a futtatásához.

1. Az eredményeket a rendszer visszaküldi az adatforrásból az átjárónak, majd az átjáró Cloud Service-nek. Az átjáró Cloud Service ezután az eredményeket használja.

### <a name="authentication-to-on-premises-data-sources"></a>Hitelesítés helyszíni adatforrásokhoz

A rendszer egy tárolt hitelesítő adatokat használ az átjáróról a helyszíni adatforrásokhoz való kapcsolódáshoz. A felhasználótól függetlenül az átjáró a tárolt hitelesítő adatokat használja a kapcsolódáshoz. Bizonyos szolgáltatások, például a DirectQuery és a LiveConnect Analysis Services esetében hitelesítési kivételek lehetnek Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

A Microsoft Cloud Services az [Azure ad](../active-directory/fundamentals/active-directory-whatis.md) használatával hitelesíti a felhasználókat. Az Azure AD-bérlő felhasználóneveket és biztonsági csoportokat tartalmaz. A bejelentkezéshez használt e-mail-cím általában megegyezik a fiók egyszerű felhasználónevével (UPN).

### <a name="what-is-my-upn"></a>Mi az UPN?

Ha nem tartományi rendszergazda, akkor előfordulhat, hogy nem ismeri az egyszerű felhasználónevet. A fiók UPN-értékének megkereséséhez futtassa a `whoami /upn` parancsot a munkaállomásról. Bár az eredmény egy e-mail-címre hasonlít, az eredmény a helyi tartományi fiók UPN-je.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Helyszíni Active Directory szinkronizálása az Azure AD-vel

A helyszíni Active Directory fiókokhoz és az Azure AD-fiókokhoz tartozó UPN-nek azonosnak kell lennie. Ezért győződjön meg arról, hogy minden helyszíni Active Directory fiók megfelel az Azure AD-fiókjának. A Cloud Services csak az Azure AD-n belüli fiókokat ismeri. Ezért nem kell hozzáadnia egy fiókot a helyszíni Active Directoryhoz. Ha a fiók nem létezik az Azure AD-ben, akkor nem használhatja ezt a fiókot.

Az alábbi módokon lehet megfelelni a helyszíni Active Directory-fiókoknak az Azure AD-vel.

* Fiókok manuális hozzáadása az Azure AD-hez.

  Hozzon létre egy fiókot a Azure Portal vagy a Microsoft 365 felügyeleti központban. Győződjön meg arról, hogy a fiók neve megegyezik a helyszíni Active Directory fiók UPN-fiókjával.

* Szinkronizálja a helyi fiókokat az Azure AD-Bérlővel az Azure Active Directory Connect eszköz használatával.

  A Azure AD Connect eszköz lehetővé teszi a címtár-szinkronizálást és a hitelesítés beállítását. Ezek közé tartozik például a jelszó-kivonat szinkronizálása, az átmenő hitelesítés és az összevonás. Ha nem bérlői rendszergazda vagy helyi tartományi rendszergazda, kérje meg a rendszergazdát, hogy állítsa be Azure AD Connect. Azure AD Connect biztosítja, hogy az Azure AD UPN megfelel a helyi Active Directory UPN-nek. Ez az egyeztetés segít, ha Analysis Services élő kapcsolatokat használ Power BI vagy egyszeri bejelentkezéses (SSO) képességekkel.

  > [!NOTE]
  > A fiókok szinkronizálása a Azure AD Connect eszközzel új fiókokat hoz létre az Azure AD-bérlőben.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Gyakori kérdések és hibaelhárítás

További információkért tekintse meg a következő témaköröket:

* [Helyszíni adatátjáró – GYIK](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Helyszíni adatátjáróval kapcsolatos hibák](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Az átjáró teljesítményének figyelése és optimalizálása](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Következő lépések

* [Kapcsolódás a helyszíni adatokhoz a Logic appsből](../logic-apps/logic-apps-gateway-connection.md)
* [Vállalati integrációs funkciók](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Az Azure Logic Apps összekötői](../connectors/apis-list.md)
