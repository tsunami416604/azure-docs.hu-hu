---
title: "Azure Active Directory-bérlőcímtárak használata – áttekintés | Microsoft Docs"
description: "Ismerteti az Azure AD-bérlő fogalmát, valamint az Azure kezelését az Azure Active Directory használatával"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro;oldportal
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 85e24587f07a4dc65a2f116499f3f7e00c3ac2b8
ms.contentlocale: hu-hu
ms.lasthandoff: 09/22/2017

---
# <a name="manage-your-azure-ad-directory"></a>Azure AD-címtár kezelése

## <a name="what-is-an-azure-ad-tenant"></a>Mi az az Azure AD-bérlő?
Az Azure Active Directoryban (Azure AD) a bérlő az Azure AD olyan dedikált példányának tekinthető, amelyet a szervezet megkap és a tulajdonában áll, amikor regisztrál egy Microsoft felhőszolgáltatásra, például az Azure-ra vagy az Office 365-re. Mindegyik Azure AD-címtár önálló, és el van választva a többi Azure AD-címtártól. Ahogy a vállalat irodaépülete is egy vállalatspecifikus biztonsági vagyontárgynak tekinthető, az Azure AD-címtár is egy, kizárólag az adott szervezet általi használatra kialakított biztonságos eszköz. Az Azure AD architektúra elkülöníti az egyes ügyfelek adatait és identitásadatait, így az adott Azure AD-címtár felhasználói és rendszergazdái véletlenül vagy kártételi szándékkal nem férhetnek hozzá más címtárak adataihoz.

![Az Azure Active-címtár kezelése](./media/active-directory-administer/aad_portals.png)

## <a name="how-can-i-get-an-azure-ad-directory"></a>Hogyan juthatok Azure AD-címtárhoz?
Az Azure AD alapvető fontosságú címtár- és identitáskezelési funkciókat biztosít a Microsoft olyan felhőszolgáltatásai számára, mint például a következők:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Azure AD-címtárhoz úgy juthat, ha regisztrál bármelyik fenti Microsoft felhőszolgáltatásra. Szükség szerint több címtárat is létrehozhat. Az első címtárat fenntarthatja például éles címtárként, majd létrehozhat egy másik címtárat is tesztelési vagy előkészítési céllal.

### <a name="using-the-azure-ad-directory-that-comes-with-a-new-azure-subscription"></a>Az új Azure-előfizetéssel kapott Azure AD-címtár használata

Javasoljuk, hogy a további Microsoft-szolgáltatásokra való regisztrációkor is az első szolgáltatáshoz használt rendszergazdai fiókot használja. A Microsoft szolgáltatásaira való első regisztráció alkalmával megadott adatok a szervezet új Azure AD-címtárpéldányának létrehozásához szükségesek. Ha más Microsoft-szolgáltatásokra való feliratkozáskor is ezt a címtárat használja a bejelentkezési kísérletek hitelesítéséhez, azok használhatják az alapértelmezett címtárban konfigurált meglévő felhasználói fiókokat, szabályzatokat, beállításokat vagy helyszíni címtár-integrációkat.

Ha például egy Microsoft Intune-előfizetésre iratkozik fel, és szinkronizálja a helyszíni Active Directoryt a saját Azure AD-címtárával, további Microsoft-szolgáltatásokra, például az Office 365-re iratkozhat fel, amely szintén a Microsoft Intune esetében már alkalmazott címtár-integrációs lehetőségeket használja ki.

További információk a helyszíni címtár Azure AD-integrációjáról: [Directory integration with Azure AD Connect](active-directory-aadconnect.md) (Címtár-integráció az Azure AD Connecttel).

### <a name="associate-an-existing-azure-ad-directory-with-a-new-azure-subscription"></a>Új Azure-előfizetés társítása meglévő Azure AD-címtárhoz
Új Azure-előfizetést társíthat ugyanahhoz a címtárhoz, amely egy meglévő Office 365- vagy Microsoft Intune-előfizetés bejelentkezésének hitelesítését végzi. Az ezzel a forgatókönyvvel kapcsolatos további információkért lásd: [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md) (Azure-előfizetés tulajdonjogának átruházása másik fiókra).

### <a name="create-an-azure-ad-directory-by-signing-up-for-a-microsoft-cloud-service-as-an-organization"></a>Azure AD-címtár létrehozása szervezeti feliratkozással egy Microsoft felhőszolgáltatásra
Ha még nem rendelkezik előfizetéssel egyetlen Microsoft felhőszolgáltatáshoz sem, a feliratkozáshoz használhatja a következő hivatkozások egyikét. Amikor először iratkozik fel egy ilyen szolgáltatásra, az Azure AD-címtár automatikusan létrejön.

* [Microsoft Azure](https://account.azure.com/organization)
* [Office 365](http://products.office.com/business/compare-office-365-for-business-plans/)
* [Microsoft Intune](https://portal.office.com/Signup/Signup.aspx?OfferId=40BE278A-DFD1-470a-9EF7-9F2596EA7FF9&dl=INTUNE_A&ali=1#0%20)

### <a name="how-to-change-the-default-directory-for-a-subscription"></a>Az előfizetés alapértelmezett könyvtárának módosítása

1. Az előfizetés tulajdonjogának átadásához jelentkezzen be az előfizetés fiókadminisztrátori fiókjával az [Azure Account Centerbe](https://account.azure.com/Subscriptions).
2. Bizonyosodjon meg róla, hogy az előfizetés tulajdonosaként megadni kívánt felhasználó szerepel a megcélzott címtárban.
3. Kattintson az **Előfizetés átadása** elemre.
4. Adja meg a címzettet. A címzett automatikusan kapni fog egy elfogadási hivatkozást tartalmazó e-mailt.
5. A címzett a hivatkozásra való kattintás után követnie kell az utasításokat, többek között a fizetési információk megadásával. Ha ez sikeresen befejeződött, a rendszer átadja az előfizetést. 
6. Az előfizetés tulajdonjogának sikeres átadása esetén az előfizetés alapértelmezett könyvtára arra módosul, amelyikben a felhasználó szerepel.

További tudnivalókért lásd: [Az Azure-előfizetés tulajdonjogának átadása másik fiók számára](../billing/billing-subscription-transfer.md)

### <a name="manage-the-default-directory-in-azure"></a>Az alapértelmezett címtár kezelése az Azure-ban
Amikor feliratkozik az Azure szolgáltatásra, a rendszer egy alapértelmezett Azure AD-címtárat társít az előfizetéséhez. Az Azure AD használata ingyenes, és a címtárak ingyenes erőforrásként használhatóak. Léteznek fizetős Azure AD szolgáltatások is, amelyek licencelése külön történik, és olyan további funkciókat biztosítanak, mint például a vállalati arculat megjelenítése a bejelentkezési felületen vagy az önkiszolgáló jelszó-visszaállítás. Egyéni tartományt is létrehozhat egy saját tulajdonú DNS-név használatával az alapértelmezett *.onmicrosoft.com tartomány helyett.

## <a name="how-can-i-manage-directory-data"></a>Hogyan történik a címtáradatok kezelése?
Egy vagy több Microsoft felhőszolgáltatás-előfizetés felügyelete esetén a szervezet címtáradatainak kezeléséhez az [Azure AD felügyeleti központot](https://aad.portal.azure.com), a Microsoft Intune-fiókportált vagy az [Office 365 felügyeleti központját](https://portal.office.com/) használhatja. Az Azure AD-ban tárolt adatok kezeléséhez az [Azure Active Directory PowerShell parancsmagjait](https://docs.microsoft.com/powershell/azure/active-directory) is használhatja.

E portálok (vagy parancsmagok) a következőket teszik lehetővé:

* Felhasználói és csoportfiókok létrehozása és kezelése
* A szervezet előfizetéséhez kapcsolódó felhőszolgáltatások felügyelete
* Helyszíni integráció beállítása az Azure AD identitás- és hitelesítési szolgáltatásaival

Az Azure AD felügyeleti központ, az Office 365 felügyeleti központ, a Microsoft Intune-fiókportál és az Azure AD-parancsmagok mind egyetlen, a szervezet címtárához társított megosztott Azure AD-példányba írnak és onnan olvasnak. Ezeknek az eszközöknek mindegyike a címtáradatok bekérésére vagy módosítására használt kezelőfelületként működik.

Ha valamelyik portál vagy parancsmag használatával módosítja a szervezeti adatokat, miközben be van jelentkezve az egyik szolgáltatásba, a változtatás a legközelebbi bejelentkezés alkalmával a többi portálon is megjelenik. Az adatokat az összes olyan Microsoft-felhőszolgáltatáson meg vannak osztva, amelyekre feliratkozott.

Ha például egy felhasználó bejelentkezésének blokkolására az Office 365 felügyeleti központját használja, ezzel az adott felhasználó bármely másik, a szervezete által aktuálisan előfizetett szolgáltatásra történő bejelentkezését is blokkolja. Ha ugyanezt a felhasználót a Microsoft Intune fiókportálján tekinti meg, ott is azt látja majd, hogy a felhasználó blokkolva van.

## <a name="how-can-i-add-and-manage-multiple-directories"></a>Hogyan vehetek fel és kezelhetek több címtárat?
[Azure AD-címtárat az Azure Portalon vehet fel](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Adja meg az adatokat, majd kattintson a **Létrehozás** gombra.

Az egyes címtárakat teljesen független erőforrásként kezelheti: mindegyikük társüzemű, minden funkcióval ellátott és logikailag független a többi, Ön által kezelt címtártól, nincs közöttük szülő-gyermek típusú kapcsolat. Ez a függetlenség az erőforrás, a felügyelet és a szinkronizálás függetlenségét is jelenti.

* **Erőforrás-függetlenség**. Ha erőforrást hoz létre vagy töröl egy címtárban, az nem érinti a többi címtárban található erőforrásokat, a külső felhasználókra vonatkozó részleges kivétellel. Ha egy címtárat egy „contoso.com” egyedi tartománnyal használja, azt egyetlen másik címtárral sem használhatja.
* **Felügyeleti függetlenség**.  Ha a „Contoso” címtár valamely nem rendszergazda felhasználója létrehoz egy „Teszt” nevű tesztelési címtárat:
  
  * A „Contoso” címtár rendszergazdájának nem lesznek közvetlen rendszergazdai jogosultságai a „Teszt” címtáron, kivéve, ha annak a rendszergazdája kifejezetten megadja számára ezeket a jogokat. A „Contoso” rendszergazdái képesek lesznek a „Teszt” címtár hozzáférésének szabályozására a címtárat létrehozó felhasználói fiók felügyeleti joga révén.
    
  * Ha egy címtárban rendszergazdai szerepkört rendel egy felhasználóhoz, illetve eltávolítja az engedélyt, a változás nem érinti az adott felhasználó más címtárakra vonatkozó rendszergazdai szerepkörét.
* **Szinkronizálási függetlenség**. Az egyes Azure AD-bérlők egymástól függetlenül konfigurálhatók adatok szinkronizálására az Azure AD Connect címtár-szinkronizáló eszköz egyetlen példányából.

A többi Azure-erőforrástól eltérően a címtárak nem egy Azure-előfizetés alsóbb szintű erőforrásai. Így az Azure-előfizetés megszüntetését vagy lejártát követően továbbra is hozzáférhet a címtár adataihoz az Azure AD PowerShell, az Azure Graph API vagy egyéb felületek, például az Office 365 felügyeleti központja használatával. A címtárhoz másik előfizetést is rendelhet.

## <a name="how-to-prepare-to-delete-an-azure-ad-directory"></a>Azure AD-címtár törlésének előkészítése
Az Azure AD-címtárakat egy globális rendszergazda törölheti a portálról. A címtárak törlésekor a bennük található összes erőforrás is törlődik. Győződjön meg arról, hogy nincs szüksége az adott címtárra, mielőtt törölné.

> [!NOTE]
> Ha a felhasználó munkahelyi vagy iskolai fiókkal jelentkezik be, a kezdőcímtárát nem szabad törölnie. Ha például a felhasználó a joe@contoso.onmicrosoft.com fiókkal van bejelentkezve, nem törölheti a contoso.onmicrosoft.com-ot alapértelmezett tartományként használó címtárat.

Egy Azure AD-címtár törléséhez bizonyos feltételeknek kell teljesülniük. Ezzel csökken annak a kockázata, hogy az adott címtár törlése kedvezőtlen hatással legyen egyes felhasználókra vagy alkalmazásokra, például az Office 365-felhasználói bejelentkezésre vagy az Azure-erőforrások hozzáférhetőségére. Ha például egy előfizetéshez tartozó címtárat véletlenül törölnek, a felhasználók ezután nem férhetnek hozzá az adott előfizetéshez tartozó Azure-erőforrásokhoz.

A rendszer a következő feltételek teljesülését ellenőrzi:

* Kizárólag egyetlen felhasználó, a globális rendszergazda törölheti a címtárat. A címtár törlése előtt az összes többi felhasználót törölni kell. Helyszíni felhasználószinkronizáláskor a művelet befejezése után a szinkronizálást ki kell kapcsolni, a felhasználókat pedig törölni kell a felhőcímtárból az Azure Portal vagy Azure PowerShell-parancsmagok használatával. Nincs szükség csoportok vagy névjegyek (például az Office 365 felügyeleti központjából felvett névjegyek) törlésére.
* A címtárban nem lehetnek alkalmazások. A címtár törlése előtt az összes alkalmazást törölni kell.
* A címtárhoz nem kapcsolhatók többtényezős hitelesítési szolgáltatók.
* Törölni kell minden, a címtárhoz társított Microsoft Online Services-előfizetést, például a Microsoft Azure-t, az Office 365-öt vagy a Prémium szintű Azure AD-t. Ha például az alapértelmezett címtár az Azure-ban lett létrehozva, nem törölheti azt mindaddig, amíg Azure-előfizetésének hitelesítése továbbra is ezen a címtáron alapul. Hasonlóképpen olyan címtárat sem törölhet, amelyhez egy másik felhasználó előfizetést társított. 


## <a name="next-steps"></a>Következő lépések
* [Azure AD fórum](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
* [Azure Multi-Factor Authentication fórum](https://social.msdn.microsoft.com/Forums/home?forum=windowsazureactiveauthentication)
* [Azure Stack Overflow-kérdések](http://stackoverflow.com/questions/tagged/azure)
* [Azure Active Directory PowerShell](https://docs.microsoft.com/powershell/azure/active-directory)
* [Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](active-directory-assign-admin-roles.md)

