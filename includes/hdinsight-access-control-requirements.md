Ha olyan Azure-előfizetést használ, amelyiknek nem a rendszergazdája vagy tulajdonosa, például a vállalat tulajdonában lévő előfizetést, akkor az ebben a dokumentumban ismertetett lépések végrehajtása előtt ellenőriznie kell az alábbiakat:

* Ha szeretne bejelentkezni az Azure-ba, legalább **Közreműködői** hozzáféréssel kell rendelkeznie ahhoz az Azure-erőforráscsoporthoz, amelyet a HDInsight (és más Azure-erőforrások) létrehozásakor használ.
* Valaki, aki legalább **Közreműködői** hozzáféréssel rendelkezik az Azure-előfizetéshez, valószínűleg korábban regisztrálta az Ön által használt erőforrás szolgáltatóját. A szolgáltató regisztrálása akkor történik meg, ha egy olyan felhasználó, aki Közreműködői hozzáféréssel rendelkezik az előfizetéshez, először hoz létre erőforrást az előfizetésben. Az erőforrás létrehozása nélkül is elvégezhető, ha [ a REST segítségével regisztrál egy szolgáltatót](https://msdn.microsoft.com/library/azure/dn790548.aspx).

A hozzáférés-kezelés használatával kapcsolatos további információk a következő dokumentumokban találhatók:

* [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../articles/active-directory/role-based-access-control-what-is.md)
* [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepkör-hozzárendelésekkel](../articles/active-directory/role-based-access-control-configure.md)

<!--HONumber=Sep16_HO5-->


