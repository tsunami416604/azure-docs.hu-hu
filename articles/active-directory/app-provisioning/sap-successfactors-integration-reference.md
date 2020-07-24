---
title: A Azure Active Directory és az SAP SuccessFactors integrációs referenciája
description: Technikai részletes betekintést az SAP SuccessFactors – HR-vezérelt kiépítés
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 07/20/2020
ms.author: chmutali
ms.openlocfilehash: 3c1d0d05554fafb4b18d8dc7043cca3e8479b35e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098380"
---
# <a name="how-azure-active-directory-provisioning-integrates-with-sap-successfactors"></a>A Azure Active Directory kiépítés integrálása az SAP SuccessFactors 

[Azure Active Directory a felhasználó-kiépítési szolgáltatás](../app-provisioning/user-provisioning.md) integrálható az [SAP SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) szolgáltatással a felhasználók identitási életciklusának kezeléséhez. A Azure Active Directory három előre elkészített integrációt kínál: 

* SuccessFactors a helyszíni Active Directory a felhasználó üzembe helyezése
* SuccessFactors Azure Active Directory a felhasználók üzembe helyezése
* SuccessFactors visszaírási

Ez a cikk bemutatja, hogyan működik az integráció, és hogyan szabhatja testre a különböző HR-forgatókönyvek kiépítési viselkedését. 

## <a name="establishing-connectivity"></a>Kapcsolat létesítése 
Az Azure AD-kiépítési motor alapszintű hitelesítést használ az Employee Central OData API-végpontokhoz való kapcsolódáshoz. Az SuccessFactors kiépítési alkalmazásának beállításakor a *rendszergazdai hitelesítő adatok* szakaszban a *bérlői URL* -cím paraméterrel konfigurálhatja az [API-adatközpont URL-címét](https://apps.support.sap.com/sap/support/knowledge/en/2215682). 

Az Azure AD kiépítési szolgáltatás és a SuccessFactors közötti kapcsolat további biztonságossá tételéhez az alábbi lépések végrehajtásával adhatja hozzá az Azure AD IP-tartományokat a SuccessFactors IP-engedélyezési listájában:

* Töltse le az Azure nyilvános felhő [legújabb IP-tartományait](https://www.microsoft.com/download/details.aspx?id=56519) 
* Nyissa meg a fájlt, és keresse meg a címkéket **AzureActiveDirectory** és **AzureActiveDirectoryDomainServices** 

  >[!div class="mx-imgBorder"] 
  >![Azure AD IP-címtartomány](media/sap-successfactors-integration-reference/azure-active-directory-ip-range.png)

* Másolja az elem *addressPrefixes* belül felsorolt összes IP-címtartományt, és használja a tartományt az IP-címek korlátozási listájának létrehozásához.
* A CIDR értékeinek lefordítása IP-tartományokra.  
* Jelentkezzen be a SuccessFactors felügyeleti portálra, és adja hozzá az IP-tartományokat az engedélyezési listához. Tekintse meg a 2253200-es SAP- [támogatási megjegyzést](https://apps.support.sap.com/sap/support/knowledge/en/2253200). Ezután [megadhatja az IP-tartományokat](https://answers.sap.com/questions/12882263/whitelisting-sap-cloud-platform-ip-address-range-i.html) ebben az eszközben. 

## <a name="supported-entities"></a>Támogatott entitások
A SuccessFactors összes felhasználója esetében az Azure AD kiépítési szolgáltatása lekéri a következő entitásokat. Minden entitás ki van bővítve a OData API *$Expand* lekérdezési paraméter használatával. Tekintse meg az alábbi *lekérési szabály* oszlopot. Egyes entitások alapértelmezés szerint ki vannak bontva, míg egyes entitások kibontása csak akkor történik meg, ha egy adott attribútum szerepel a leképezésben. 

| \# | SuccessFactors entitás                  | OData csomópont     | Lekérési szabály |
|----|----------------------------------------|------------------------------|------------------|
| 1  | PerPerson                              | *gyökérszintű csomópont*                  | Mindig           |
| 2  | PerPersonal                            | personalInfoNav              | Mindig           |
| 3  | PerPhone                               | phoneNav                     | Mindig           |
| 4  | PerEmail                               | emailNav                     | Mindig           |
| 5  | EmpEmployment                          | employmentNav                | Mindig           |
| 6  | Felhasználó                                   | employmentNav/userNav        | Mindig           |
| 7  | EmpJob                                 | employmentNav/jobInfoNav     | Mindig           |
| 8  | EmpEmploymentTermination               | activeEmploymentsCount       | Mindig           |
| 9  | FOCompany                              | employmentNav/jobInfoNav/companyNav | Csak akkor, ha a vállalat vagy a companyId attribútum le van képezve |
| 10 | FODepartment                           | employmentNav/jobInfoNav/departmentNav | Csak akkor, ha az osztály vagy a departmentId attribútum le van képezve |
| 11 | FOBusinessUnit                         | employmentNav/jobInfoNav/businessUnitNav | Csak akkor, ha a részleghez vagy a businessUnitId attribútum le van képezve |
| 12 | FOCostCenter                           | employmentNav/jobInfoNav/costCenterNav | Csak akkor, ha a costCenter vagy a costCenterId attribútum le van képezve |
| 13 | FODivision                             | employmentNav/jobInfoNav/divisionNav  | Csak akkor, ha az osztás vagy a divisionId attribútum le van képezve |
| 14 | FOJobCode                              | employmentNav/jobInfoNav/jobCodeNav  | Csak akkor, ha a jobCode vagy a jobCodeId attribútum le van képezve |
| 15 | FOPayGrade                             | employmentNav/jobInfoNav/payGradeNav  | Csak akkor, ha a payGrade attribútum le van képezve |
| 16 | FOLocation                             | employmentNav/jobInfoNav/locationNav  | Csak akkor, ha a Location attribútum le van képezve |
| 17 | FOCorporateAddressDEFLT                | employmentNav/jobInfoNav/addressNavDEFLT  | Ha a leképezés a következő attribútumok egyikét tartalmazza: officeLocationAddress, officeLocationCity, officeLocationZipCode |
| 18 | FOEventReason                          | employmentNav/jobInfoNav/eventReasonNav  | Csak akkor, ha a eventReason attribútum le van képezve |
| 19 | EmpGlobalAssignment                    | employmentNav/empGlobalAssignmentNav | Csak akkor, ha assignmentType van leképezve |
| 20 | EmploymentType-lista                | employmentNav/jobInfoNav/employmentTypeNav | Csak akkor, ha employmentType van leképezve |
| 21 | EmployeeClass-lista                 | employmentNav/jobInfoNav/employeeClassNav | Csak akkor, ha employeeClass van leképezve |
| 22 | EmplStatus-lista                    | employmentNav/jobInfoNav/emplStatusNav | Csak akkor, ha emplStatus van leképezve |
| 23 | AssignmentType-lista                | employmentNav/empGlobalAssignmentNav/assignmentTypeNav | Csak akkor, ha assignmentType van leképezve |

## <a name="how-full-sync-works"></a>A teljes szinkronizálás működése
Az attribútumok leképezése alapján a teljes szinkronizálás során az Azure AD-létesítési szolgáltatás az alábbi "GET" OData API-lekérdezést küldi le az összes aktív felhasználó tényleges adatának beolvasásához. 

> [!div class="mx-tdCol2BreakAll"]
>| Paraméter | Leírás |
>| ----------|-------------|
>| OData API-gazda | Https hozzáfűzése a *bérlői URL-címhez*. Például: `https://api4.successfactors.com` |
>| OData API-végpont | `/odata/v2/PerPerson` |
>| OData $format lekérdezési paraméter | `json` |
>| OData $filter lekérdezési paraméter | `(personEmpTerminationInfoNav/activeEmploymentsCount ge 1) and (lastModifiedDateTime le <CurrentExecutionTime>)` |
>| OData $expand lekérdezési paraméter | A paraméter értéke a leképezett attribútumoktól függ. Például: `employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,phoneNav,emailNav,employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav` |
>| OData customPageSize lekérdezési paramétere | `100` |

> [!NOTE]
> Az első teljes szinkronizálás során az Azure AD kiépítési szolgáltatása nem kéri le az inaktív/megszakított feldolgozói adataikat.

Az egyes SuccessFactors-felhasználók esetében a kiépítési szolgáltatás a leképezésben definiált egyező attribútum használatával keres egy fiókot a célhelyen (Azure AD/helyszíni Active Directory). Például: Ha a *personIdExternal* az *AlkalmazottKód* értékre van beállítva, és az egyező attribútumként van megadva, akkor a kiépítési szolgáltatás a *personIdExternal* értéket használja az *AlkalmazottKód* -szűrővel rendelkező felhasználó kereséséhez. Ha egy felhasználói egyezés található, akkor frissíti a célként megadott attribútumokat. Ha nem talál egyezést, egy új bejegyzést hoz létre a célhelyen. 

Egy adott *personIdExternal*tartozó OData API-végpont által visszaadott adatellenőrzéshez frissítse a *SUCCESSFACTORSAPIENDPOINT* az alábbi API-lekérdezésben az API-adatközpont-kiszolgáló URL-címével, és használjon olyan eszközt, mint a [Poster](https://www.postman.com/downloads/) a lekérdezés meghívásához. 

```
https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
$filter=(personIdExternal in '[personIdExternalValue]')&
$expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

## <a name="how-incremental-sync-works"></a>A növekményes szinkronizálás működése

A teljes szinkronizálás után az Azure AD kiépítési szolgáltatása fenntartja a *LastExecutionTimestamp* , és a használatával különbözeti lekérdezéseket hoz létre a növekményes módosítások beolvasásához. Az egyes SuccessFactors entitásokban (például a *createdondatetime*, a *StartDate*, a *endDate*és a *latestTerminationDate*) található időbélyeg-attribútumok kiértékelésével megtekintheti, hogy a változás a *LastExecutionTimestamp* és a *CurrentExecutionTime*között esik-e. Ha igen, akkor a bejegyzés változása érvényesnek minősül, és szinkronizálásra lesz feldolgozva. 

## <a name="reading-attribute-data"></a>Attribútum-információk olvasása

Amikor az Azure AD-létesítési szolgáltatás lekérdezi a SuccessFactors, egy JSON-eredményhalmazt kér le. A JSON-eredményhalmaz számos attribútumot tartalmaz, amelyeket az Employee Central tárol. Alapértelmezés szerint a kiépítési séma úgy van konfigurálva, hogy csak az attribútumok egy részhalmazát kérje le. 

További attribútumok lekéréséhez kövesse az alábbi lépéseket:
    
* Tallózással keresse meg a **nagyvállalati alkalmazásokat**az SuccessFactors kiépítési attribútum-  ->  **SuccessFactors App**  ->  **Provisioning**  ->  **Edit Provisioning**  ->  **hozzárendelési oldalának szerkesztése oldalon**.
* Görgessen le, és kattintson a **Speciális beállítások megjelenítése**elemre.
* Kattintson az **attribútumok szerkesztése SuccessFactors**elemre. 

> [!NOTE] 
> Ha az **attribútumok szerkesztése SuccessFactors** beállítás nem jelenik meg a Azure Portalban, használja az URL-címet az *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* oldal eléréséhez. 

* A nézet **API-kifejezés** oszlopa az összekötő által használt JSONPath-kifejezéseket jeleníti meg.
  >[!div class="mx-imgBorder"] 
  >![API-kifejezés](media/sap-successfactors-integration-reference/jsonpath-api-expressions.png#lightbox)  
* Szerkesztheti a meglévő JSONPath értékét, vagy hozzáadhat egy új attribútumot érvényes JSONPath-kifejezéssel a sémához. 

A következő szakasz felsorolja a JSONPath-értékek szerkesztésének gyakori forgatókönyveit. 

## <a name="handling-different-hr-scenarios"></a>Különböző HR-forgatókönyvek feldolgozása

A JSONPath a JSON lekérdezési nyelve, amely az XML XPath-hoz hasonló. Az XPath-hoz hasonlóan a JSONPath lehetővé teszi az adatok JSON-adattartalomból való kinyerését és szűrését.
A JSONPath-átalakítás használatával testre szabhatja az Azure AD-kiépítési alkalmazás viselkedését egyéni attribútumok lekérésére és olyan forgatókönyvek kezelésére, mint például a Felhelyezés, a feldolgozó átalakítása és a globális hozzárendelés. 

Ez a szakasz ismerteti, hogyan szabhatja testre a kiépítési alkalmazást a következő HR-forgatókönyvekhez: 
* [További attribútumok beolvasása](#retrieving-additional-attributes)
* [Egyéni attribútumok beolvasása](#retrieving-custom-attributes)
* [A Worker átalakítási forgatókönyvének feldolgozása](#handling-worker-conversion-scenario)
* [Az újrafelvételi forgatókönyvek feldolgozása](#handling-rehire-scenario)
* [Globális hozzárendelési forgatókönyvek kezelésére](#handling-global-assignment-scenario)
* [Egyidejű feladatok kezelésére szolgáló forgatókönyv](#handling-concurrent-jobs-scenario)

### <a name="retrieving-additional-attributes"></a>További attribútumok beolvasása

Az Azure AD SuccessFactors alapértelmezett kiépítési alkalmazási sémája [90 + előre definiált attribútumokkal](sap-successfactors-attribute-reference.md)rendelkezik. A kiépítési sémához tartozó további SuccessFactors-attribútumok hozzáadásához kövesse az alábbi lépéseket: 

* Az alábbi OData-lekérdezéssel kérhet le egy érvényes tesztelési felhasználó adatait az Employee Central szolgáltatásból. 

```
    https://[SuccessFactorsAPIEndpoint]/odata/v2/PerPerson?$format=json&
    $filter=(personIdExternal in '[personIdExternalValue]')&
    $expand=employmentNav/userNav,employmentNav/jobInfoNav,personalInfoNav,personEmpTerminationInfoNav,
    phoneNav,phoneNav/phoneTypeNav,emailNav,employmentNav/jobInfoNav/businessUnitNav,employmentNav/jobInfoNav/companyNav,
    employmentNav/jobInfoNav/companyNav/countryOfRegistrationNav,employmentNav/jobInfoNav/costCenterNav,
    employmentNav/jobInfoNav/divisionNav,employmentNav/jobInfoNav/departmentNav,employmentNav/jobInfoNav/jobCodeNav,
    employmentNav/jobInfoNav/locationNav,employmentNav/jobInfoNav/locationNav/addressNavDEFLT,employmentNav/jobInfoNav/payGradeNav,
    employmentNav/empGlobalAssignmentNav,employmentNav/empGlobalAssignmentNav/assignmentTypeNav,employmentNav/jobInfoNav/emplStatusNav,
    employmentNav/jobInfoNav/employmentTypeNav,employmentNav/jobInfoNav/employeeClassNav,employmentNav/jobInfoNav/eventReasonNav
```

* Az attribútumhoz rendelt alkalmazott központi entitás meghatározása
  * Ha az attribútum a *EmpEmployment* entitás részét képezi, keresse meg az attribútumot az *employmentNav* csomópont alatt. 
  * Ha az attribútum a *felhasználói* entitás része, keresse meg az attribútumot a *employmentNav/userNav* csomópont alatt.
  * Ha az attribútum a *EmpJob* entitás részét képezi, keresse meg az attribútumot a *employmentNav/jobInfoNav* csomópont alatt. 
* Hozza létre az attribútumhoz társított JSON-útvonalat, és adja hozzá ezt az új attribútumot a SuccessFactors-attribútumok listájához. 
  * 1. példa: tegyük fel, hogy hozzá kívánja adni a *okToRehire*attribútumot, amely a *employmentNav* entitás részét képezi, majd használja a JSONPath`$.employmentNav.results[0].okToRehire`
  * 2. példa: tegyük fel, hogy hozzá szeretné adni az *userNav* entitás részét képező *időzóna-időzónát*, majd használja a JSONPath`$.employmentNav.results[0].userNav.timeZone`
  * 3. példa: tegyük fel, hogy hozzá kívánja adni a *flsaStatus*attribútumot, amely a *jobInfoNav* entitás részét képezi, majd használja a JSONPath`$.employmentNav.results[0].jobInfoNav.results[0].flsaStatus`
* Mentse a sémát. 
* Indítsa újra az üzembe helyezést.

### <a name="retrieving-custom-attributes"></a>Egyéni attribútumok beolvasása

Alapértelmezés szerint a következő egyéni attribútumok előre definiálva vannak az Azure AD SuccessFactors üzembe helyezési alkalmazásban: 
* *custom01 – custom15* a felhasználó (userNav) entitásból
* *customString1 – customString15* a EmpEmployment (employmentNav) nevű entitásból, amelynek neve *empNavCustomString1-empNavCustomString15*
* *customString1 – customString15* a EmpJobInfo (jobInfoNav) nevű entitásból, amelynek neve *empJobNavCustomString1-empNavJobCustomString15*

Tegyük fel, hogy az alkalmazott központi példányában a *EmpJobInfo* *customString35* attribútuma tárolja a hely leírását. Ezt az értéket át szeretné állítani Active Directory *physicalDeliveryOfficeName* attribútumra. Ehhez a forgatókönyvhöz az attribútumok leképezésének konfigurálásához kövesse az alábbi lépéseket: 

* Szerkessze a SuccessFactors attribútum listáját, és vegyen fel egy *empJobNavCustomString35*nevű új attribútumot.
* Állítsa be a JSONPath API-kifejezést ehhez az attribútumhoz a következőként:`$.employmentNav.results[0].jobInfoNav.results[0].customString35`
* Mentse és töltse be újra a megfeleltetés változását a Azure Portalban.  
* Az attribútum-hozzárendelés panelen a Térkép *EmpJobNavCustomString35* *physicalDeliveryOfficeName*.
* Mentse a leképezést.

A forgatókönyv kiterjesztése: 
* Ha a *custom35* attribútumot a *felhasználói* entitásból szeretné leképezni, akkor használja a JSONPath`$.employmentNav.results[0].userNav.custom35`
* Ha a *customString35* attribútumot a *EmpEmployment* entitásból szeretné leképezni, akkor használja a JSONPath`$.employmentNav.results[0].customString35`

### <a name="handling-worker-conversion-scenario"></a>A Worker átalakítási forgatókönyvének feldolgozása

A feldolgozó átalakítása egy meglévő teljes munkavégző alkalmazott átalakítása egy alvállalkozóra vagy fordítva. Ebben a forgatókönyvben az Employee Central új *EmpEmployment* entitást hoz létre, és egy új *felhasználói* entitást hoz létre ugyanahhoz a *személy* entitáshoz. Az előző *EmpEmployment* entitásban beágyazott *felhasználói* entitás NULL értékűre van állítva. Ha úgy szeretné kezelni ezt a forgatókönyvet, hogy az új munkafolyamati adatai megjelenjenek az átalakítás során, az alábbi lépésekkel tömegesen frissítheti a kiépítési alkalmazás sémáját:  

* Nyissa meg az SuccessFactors kiépítési alkalmazásának attribútum-hozzárendelési paneljét. 
* Görgessen le, és kattintson a **Speciális beállítások megjelenítése**elemre.
* Az itt található hivatkozásra kattintva **megtekintheti** a séma-szerkesztőt. 
  >![séma áttekintése](media/sap-successfactors-integration-reference/review-schema.png#lightbox)
* A **Letöltés** hivatkozásra kattintva mentheti a séma másolatát a Szerkesztés előtt. 
  >![Letöltés – séma](media/sap-successfactors-integration-reference/download-schema.png#lightbox)
* A sémakezelő szerkesztőben nyomja le a CTRL-H billentyűt a Find-replace vezérlő megnyitásához.
* A keresés szövegmezőbe másolja és illessze be az értéket.`$.employmentNav.results[0]`
* A csere szövegmezőbe másolja és illessze be az értéket `$.employmentNav.results[?(@.userNav != null)]` . Jegyezze fel az `!=` operátort körülvevő szóközt, ami fontos a JSONPath kifejezés sikeres feldolgozásához. 
  >![Keresés – csere – konverzió](media/sap-successfactors-integration-reference/find-replace-conversion-scenario.png#lightbox)
* Kattintson az "összes cseréje" lehetőségre a séma frissítéséhez. 
* Mentse a sémát. 
* A fenti folyamat az alábbi módon frissíti az összes JSONPath-kifejezést: 
  * Régi JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Új JSONPath:`$.employmentNav.results[?(@.userNav != null)].jobInfoNav.results[0].departmentNav.name_localized`
* Indítsa újra az üzembe helyezést. 

### <a name="handling-rehire-scenario"></a>Az újrafelvételi forgatókönyvek feldolgozása

Az újrafelvételek feldolgozására általában két lehetőség áll rendelkezésre: 
* 1. lehetőség: új személy profil létrehozása az Employee Central-ban
* 2. lehetőség: a meglévő személy profiljának újbóli felhasználása az Employee Central-ban

Ha a HR-folyamat az 1. lehetőséget használja, akkor a létesítési sémához nem szükséges módosítás. Ha a HR-folyamat 2. lehetőséget használ, akkor az Employee Central új *EmpEmployment* entitást hoz létre, és egy új *felhasználói* entitást hoz létre ugyanahhoz a *személy* entitáshoz. Az átalakítási forgatókönyvtől eltérően az előző *EmpEmployment* entitás megőrzi a *felhasználói* entitást, és nem NULL értékűre van állítva. 

A rehires forgatókönyv (2. lehetőség) kezeléséhez, hogy a legújabb foglalkoztatási adatai megjelenjenek a felhasználható profilok esetében, az alábbi lépésekkel tömegesen frissítheti a kiépítési alkalmazás sémáját:  

* Nyissa meg az SuccessFactors kiépítési alkalmazásának attribútum-hozzárendelési paneljét. 
* Görgessen le, és kattintson a **Speciális beállítások megjelenítése**elemre.
* Az itt található hivatkozásra kattintva **megtekintheti** a séma-szerkesztőt.   
* A **Letöltés** hivatkozásra kattintva mentheti a séma másolatát a Szerkesztés előtt.   
* A sémakezelő szerkesztőben nyomja le a CTRL-H billentyűt a Find-replace vezérlő megnyitásához.
* A keresés szövegmezőbe másolja és illessze be az értéket.`$.employmentNav.results[0]`
* A csere szövegmezőbe másolja és illessze be az értéket `$.employmentNav.results[-1:]` . Ez a JSONPath-kifejezés a legújabb *EmpEmployment* -rekordot adja vissza.   
* Kattintson az "összes cseréje" lehetőségre a séma frissítéséhez. 
* Mentse a sémát. 
* A fenti folyamat az alábbi módon frissíti az összes JSONPath-kifejezést: 
  * Régi JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Új JSONPath:`$.employmentNav.results[-1:].jobInfoNav.results[0].departmentNav.name_localized`
* Indítsa újra az üzembe helyezést. 

### <a name="handling-global-assignment-scenario"></a>Globális hozzárendelési forgatókönyvek kezelésére

Ha az alkalmazotti központ egy felhasználóját a globális hozzárendeléshez dolgozza fel, a SuccessFactors egy új *EmpEmployment* -entitást ad hozzá, és beállítja a *ASSIGNMENTCLASS* a "ga" értékre. Emellett új *felhasználói* entitást is létrehoz. Így a felhasználónak most a következőket kell tennie:
* Egy *EmpEmployment*  +  *felhasználói* entitás, amely a Kezdőlap hozzárendelésnek felel meg, a *assignmentClass* beállítás értéke "St" és 
* Egy másik *EmpEmployment*  +  *felhasználói* entitás, amely megfelel a globális hozzárendelésnek a "ga" értékre beállított *assignmentClass*

A standard hozzárendelés és a globális hozzárendelés felhasználói profilhoz tartozó attribútumok beolvasásához kövesse az alábbi lépéseket: 

* Nyissa meg az SuccessFactors kiépítési alkalmazásának attribútum-hozzárendelési paneljét. 
* Görgessen le, és kattintson a **Speciális beállítások megjelenítése**elemre.
* Az itt található hivatkozásra kattintva **megtekintheti** a séma-szerkesztőt.   
* A **Letöltés** hivatkozásra kattintva mentheti a séma másolatát a Szerkesztés előtt.   
* A sémakezelő szerkesztőben nyomja le a CTRL-H billentyűt a Find-replace vezérlő megnyitásához.
* A keresés szövegmezőbe másolja és illessze be az értéket.`$.employmentNav.results[0]`
* A csere szövegmezőbe másolja és illessze be az értéket `$.employmentNav.results[?(@.assignmentClass == 'ST')]` . 
* Kattintson az "összes cseréje" lehetőségre a séma frissítéséhez. 
* Mentse a sémát. 
* A fenti folyamat az alábbi módon frissíti az összes JSONPath-kifejezést: 
  * Régi JSONPath:`$.employmentNav.results[0].jobInfoNav.results[0].departmentNav.name_localized`
  * Új JSONPath:`$.employmentNav.results[?(@.assignmentClass == 'ST')].jobInfoNav.results[0].departmentNav.name_localized`
* Töltse be újra az alkalmazás attribútum-hozzárendelési paneljét. 
* Görgessen le, és kattintson a **Speciális beállítások megjelenítése**elemre.
* Kattintson az **attribútumok szerkesztése SuccessFactors**elemre.
* Adja hozzá az új attribútumokat a globális hozzárendelési adat beolvasásához. Például: Ha egy globális hozzárendelési profillal társított részleg nevét szeretné beolvasni, adja hozzá a **globalAssignmentDepartment** attribútumot a JSONPath kifejezéshez `$.employmentNav.results[?(@.assignmentClass == 'GA')].jobInfoNav.results[0].departmentNav.name_localized` . 
* Most már két részleg értékeit is elvégezheti Active Directory attribútumokra, vagy szelektíven átadhat egy értéket a kifejezés-hozzárendelés használatával. Példa: az alábbi kifejezés azt állítja be, hogy az AD *Department* attribútum értéke *globalAssignmentDepartment* , ha van, akkor az értéket a normál hozzárendeléshez társított *részlegre* állítja be. 
  * `IIF(IsPresent([globalAssignmentDepartment]),[globalAssignmentDepartment],[department])`
* Mentse a leképezést. 
* Indítsa újra az üzembe helyezést. 

### <a name="handling-concurrent-jobs-scenario"></a>Egyidejű feladatok kezelésére szolgáló forgatókönyv

Ha az alkalmazotti központ egyik felhasználója egyidejűleg/több feladatot is tartalmaz, két *EmpEmployment* és *felhasználói* entitás létezik, és a *assignmentClass* beállítása "St". A mindkét feladathoz tartozó attribútumok lekéréséhez kövesse az alábbi lépéseket: 

* Nyissa meg az SuccessFactors kiépítési alkalmazásának attribútum-hozzárendelési paneljét. 
* Görgessen le, és kattintson a **Speciális beállítások megjelenítése**elemre.
* Kattintson az **attribútumok szerkesztése SuccessFactors**elemre.
* Tegyük fel, hogy az 1. feladatokhoz és a 2. feladatokhoz tartozó részleget szeretné lekérni. Az előre definiált attribútum *részleg* már beolvassa az első feladatokhoz tartozó részleg értékét. Megadhat egy *secondJobDepartment* nevű új attribútumot, és a JSONPath kifejezést a következőre állíthatja`$.employmentNav.results[1].jobInfoNav.results[0].departmentNav.name_localized`
* Most már két részleg értékeit is elvégezheti Active Directory attribútumokra, vagy szelektíven átadhat egy értéket a kifejezés-hozzárendelés használatával. 
* Mentse a leképezést. 
* Indítsa újra az üzembe helyezést. 

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan konfigurálhatja a SuccessFactors Active Directory kiépítés céljából](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Ismerje meg, hogyan konfigurálhatja a visszaírási a SuccessFactors-be](../saas-apps/sap-successfactors-writeback-tutorial.md)
* [További információ a bejövő kiépítés támogatott SuccessFactors attribútumairól](sap-successfactors-attribute-reference.md)










