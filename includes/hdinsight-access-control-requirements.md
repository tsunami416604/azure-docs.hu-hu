Használhat olyat Azure-előfizetést, amelynek nem rendszergazdája vagy tulajdonosa (például vállalati tulajdonú előfizetés). Ebben az esetben ellenőriznie kell a következők meglétét, hogy követhesse a jelen cikkben szereplő lépéseket:

* Közreműködői hozzáférés. Az Azure-ba történő bejelentkezéshez Önnek legalább Közreműködői hozzáféréssel kell rendelkeznie az Azure-erőforráscsoporthoz. Ezt az erőforráscsoportot HDInsight-fürtök és egyéb Azure-erőforrások létrehozására lehet felhasználni.
* Szolgáltatói regisztráció. Egy, az Azure-előfizetéshez legalább Közreműködői hozzáféréssel rendelkező személynek korábban regisztrálnia kellett az Ön által használt erőforrás szolgáltatóját. A szolgáltató regisztrálása akkor történik meg, ha egy olyan felhasználó, aki Közreműködői hozzáféréssel rendelkezik az előfizetéshez, először hoz létre erőforrást az előfizetésben. Ez a lépés egy erőforrás létrehozása nélkül is elvégezhető, [ha a REST-en keresztül regisztrálnak egy szolgáltatót](https://msdn.microsoft.com/library/azure/dn790548.aspx).

A hozzáférés-kezelés használatával kapcsolatban a következő cikkekben talál további tájékoztatást:

* [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../articles/active-directory/role-based-access-control-what-is.md)
* [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../articles/active-directory/role-based-access-control-configure.md)
