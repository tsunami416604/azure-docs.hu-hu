---
title: SAP SuccessFactors attribútumhivatkozás | Microsoft dokumentumok
description: Ismerje meg, hogy a SuccessFactors mely attribútumait támogatja a SuccessFactors-HR alapú kiépítés
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 00b16f969525e7b802c008ba247ecba015875689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522356"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors attribútumhivatkozás

## <a name="supported-successfactors-entities-and-attributes"></a>Támogatott SuccessFactors entitások és attribútumok

Az alábbi táblázat a következő két kiépítési alkalmazás által támogatott SuccessFactors attribútumok listáját tartalmazza: 
* [SuccessFactors az Active Directory felhasználói kiépítéséhez](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [SuccessFactors az Azure AD-felhasználók kiépítéséhez](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | SuccessFactors entitás                  | SuccessFactors attribútum     | Művelettípus |
|----|----------------------------------------|------------------------------|----------------|
| 1  | Perperson                              | personIdKülső             | Olvasás           |
| 2  | Perperson                              | personId                     | Olvasás           |
| 3  | Perperson                              | perPersonUuid                | Olvasás           |
| 4  | PerPersonal                            | displayName                  | Olvasás           |
| 5  | PerPersonal                            | firstName                    | Olvasás           |
| 6  | PerPersonal                            | gender                       | Olvasás           |
| 7  | PerPersonal                            | lastName                     | Olvasás           |
| 8  | PerPersonal                            | middleName (középső név)                   | Olvasás           |
| 9  | PerPersonal                            | preferredName (elsődleges név)                | Olvasás           |
| 10 | Felhasználó                                   | addressLine1                 | Olvasás           |
| 11 | Felhasználó                                   | addressLine2                 | Olvasás           |
| 12 | Felhasználó                                   | címLIne3                 | Olvasás           |
| 13 | Felhasználó                                   | üzletiPhone                | Olvasás           |
| 14 | Felhasználó                                   | Mobiltelefon                    | Olvasás           |
| 15 | Felhasználó                                   | city                         | Olvasás           |
| 16 | Felhasználó                                   | ország                      | Olvasás           |
| 17 | Felhasználó                                   | egyéni01                     | Olvasás           |
| 18 | Felhasználó                                   | egyéni02                     | Olvasás           |
| 19 | Felhasználó                                   | egyéni03                     | Olvasás           |
| 20 | Felhasználó                                   | egyéni04                     | Olvasás           |
| 21 | Felhasználó                                   | egyéni05                     | Olvasás           |
| 22 | Felhasználó                                   | egyéni06                     | Olvasás           |
| 23 | Felhasználó                                   | egyéni07                     | Olvasás           |
| 24 | Felhasználó                                   | egyéni08                     | Olvasás           |
| 25 | Felhasználó                                   | egyéni09                     | Olvasás           |
| 26 | Felhasználó                                   | egyéni10                     | Olvasás           |
| 27 | Felhasználó                                   | egyéni11                     | Olvasás           |
| 28 | Felhasználó                                   | egyéni12                     | Olvasás           |
| 29 | Felhasználó                                   | egyéni13                     | Olvasás           |
| 30 | Felhasználó                                   | egyéni14                     | Olvasás           |
| 31 | Felhasználó                                   | empId között                        | Olvasás           |
| 32 | Felhasználó                                   | otthoni telefon                    | Olvasás           |
| 33 | Felhasználó                                   | jobFamily (munkaCsalád)                    | Olvasás           |
| 34 | Felhasználó                                   | Felhasználónév                     | Olvasás           |
| 35 | Felhasználó                                   | state                        | Olvasás           |
| 36 | Felhasználó                                   | timeZone                     | Olvasás           |
| 37 | Felhasználó                                   | felhasználónév                     | Olvasás           |
| 38 | Felhasználó                                   | irányítószám                      | Olvasás           |
| 39 | PerPhone                               | areaCode                     | Olvasás           |
| 40 | PerPhone                               | országKód                  | Olvasás           |
| 41 | PerPhone                               | Kiterjesztés                    | Olvasás           |
| 42 | PerPhone                               | phoneNumber (telefonszám)                  | Olvasás           |
| 43 | PerPhone                               | phoneType                    | Olvasás           |
| 44 | PerEmail (PerEmail)                               | e-mailcím                 | Olvasás, írás    |
| 45 | PerEmail (PerEmail)                               | e-mailType                    | Olvasás           |
| 46 | EmpFoglalkoztatás                          | firstDate Worked              | Olvasás           |
| 47 | EmpFoglalkoztatás                          | lastDate Worked               | Olvasás           |
| 48 | EmpFoglalkoztatás                          | userId                       | Olvasás           |
| 49 | EmpFoglalkoztatás                          | isContingentWorker           | Olvasás           |
| 50 | EmpJob között                                 | countryOfCompany             | Olvasás           |
| 51 | EmpJob között                                 | emplStatus                   | Olvasás           |
| 52 | EmpJob között                                 | endDate (dátum)                      | Olvasás           |
| 53 | EmpJob között                                 | kezdődátum                    | Olvasás           |
| 54 | EmpJob között                                 | jobTitle                     | Olvasás           |
| 55 | EmpJob között                                 | pozíció                     | Olvasás           |
| 65 | EmpJob között                                 | egyéni Karakterlánc13               | Olvasás           |
| 56 | EmpJob között                                 | kezelőazonosító                    | Olvasás           |
| 57 | EmpJob\.Üzleti Egység                   | üzleti egység                 | Olvasás           |
| 58 | EmpJob\.Üzleti Egység                   | businessUnitId               | Olvasás           |
| 59 | EmpJob\.Vállalat                        | cég                      | Olvasás           |
| 60 | EmpJob\.Vállalat                        | companyId                    | Olvasás           |
| 61 | EmpJob\.\.Cég CountryOfRegistration | twoCharCountryCode           | Olvasás           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | Olvasás           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | Olvasás           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | Olvasás           |
| 65 | EmpJob\.osztály                     | Részleg                   | Olvasás           |
| 66 | EmpJob\.osztály                     | departmentId                 | Olvasás           |
| 67 | EmpJob\.divízió                       | Osztály                     | Olvasás           |
| 68 | EmpJob\.divízió                       | divízióId                   | Olvasás           |
| 69 | EmpJob\.feladatkód                        | feladatkód                      | Olvasás           |
| 70 | EmpJob\.feladatkód                        | jobCodeId                    | Olvasás           |
| 71 | EmpJob\.hely                       | Helyneve                 | Olvasás           |
| 72 | EmpJob\.hely                       | officeLocationAddress cím        | Olvasás           |
| 73 | EmpJob\.hely                       | officeLocationCity           | Olvasás           |
| 74 | EmpJob\.hely                       | officeLocationCustomString4  | Olvasás           |
| 75 | EmpJob\.hely                       | officeLocationZipCode        | Olvasás           |
| 76 | EmpJob\.PayGrade                       | payGrade (fizetős besorolású)                     | Olvasás           |
| 77 | EmpEmploymentFelmondás               | activeEmploymentsCount       | Olvasás           |
| 78 | EmpEmploymentFelmondás               | legutóbbi Elévülésdátuma        | Olvasás           |


## <a name="default-attribute-mapping"></a>Alapértelmezett attribútumleképezés

Az alábbi táblázat a fent felsorolt SuccessFactors attribútumok és az AD/Azure AD-attribútumok közötti alapértelmezett attribútumleképezést tartalmazza. Az Azure AD kiépítési alkalmazás "Mapping" panelen módosíthatja ezt az alapértelmezett leképezést, hogy a fenti listából származó attribútumokat tartalmazzon. 

| \# | SuccessFactors entitás                  | SuccessFactors attribútum | Alapértelmezett AD/Azure AD attribútumleképezés   | Megjegyzés feldolgozása                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | Perperson                              | personIdKülső         | alkalmazottazonosító                              | Egyező attribútumként használva                                                                   |
| 2  | Perperson                              | perPersonUuid            | \[Nincs leképezve \- forráshorgonyként\] | A kezdeti szinkronizálás során a létesítési szolgáltatás a personUuid-ot a meglévő objectGuid\-hez kapcsolja.  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | Felhasználó                                   | addressLine1             | utcacím                           | NA                                                                                           |
| 7  | Felhasználó                                   | city                     | l                                       | NA                                                                                           |
| 8  | Felhasználó                                   | ország                  | Co                                      | NA                                                                                           |
| 9  | Felhasználó                                   | state                    | st                                      | NA                                                                                           |
| 10 | Felhasználó                                   | felhasználónév                 | samAccountName                          | NA                                                                                           |
| 11 | Felhasználó                                   | irányítószám                  | postai kód                              | NA                                                                                           |
| 12 | PerEmail (PerEmail)                               | e-mailcím             | Levelezés                                    | NA                                                                                           |
| 13 | EmpJob között                                 | jobTitle                 | cím                                   | NA                                                                                           |
| 14 | EmpJob között                                 | kezelőazonosító                | manager                                 | NA                                                                                           |
| 15 | EmpJob\.\.Cég CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob\.osztály                     | Részleg               | Részleg                              | NA                                                                                           |
| 17 | EmpJob\.divízió                       | Osztály                 | cég                                 | NA                                                                                           |
| 18 | EmpJob\.hely                       | officeLocationAddress cím    | utcacím                           | NA                                                                                           |
| 19 | EmpJob\.hely                       | officeLocationZipCode    | postai kód                              | NA                                                                                           |
| 20 | EmpEmploymentFelmondás               | activeEmploymentsCount   | accountEnabled                          | ha activeEmploymentsCount=0, tiltsa le a fiókot\.                                           |

