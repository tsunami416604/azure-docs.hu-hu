**Új CDN-profil létrehozása**

1. Az a [Azure-portálon](https://portal.azure.com), válassza a bal felső **hozzon létre egy erőforrást**.
    
2. Az **Új** panelen válassza a **Web + mobil**, majd a **CDN** lehetőséget.
   
    A **CDN-profil** panel jelenik meg.
   
    ![Új CDN-profil](./media/cdn-create-profile/new-cdn-profile-include.png)
3. A **neve**, adjon meg egy egyedi nevet a CDN-profilt.
    
4. A **előfizetés**, válassza ki az előfizetést a CDN-profil használatára.
   
5. A **erőforráscsoport**, válassza ki vagy hozzon létre egy erőforráscsoportot. Erőforráscsoportok kapcsolatos információkért lásd: [Azure Resource Manager áttekintése](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
    
6. A **erőforráscsoport helye**, válassza ki az Azure-hely a CDN-profil adatait tárolja. Ezen a helyen nincs hatással van a CDN-végpontok helyére.
    
7. A **tarifacsomag**, válassza ki a réteget. Az egyes tarifacsomagok funkcióit, lásd: [Azure CDN szolgáltatásai](../articles/cdn/cdn-overview.md#azure-cdn-features).
   
    ![CDN-tarifacsomag kiválasztása](./media/cdn-create-profile/cdn-choose-sku-include.png)

8. Bejelölheti **most hozzon létre egy új CDN-végpont** jelenítheti meg a végpont létrehozásához, amely a profilt hoz létre egy időben. További információkért lásd: [hozzon létre egy új CDN-végpont](../articles/cdn/cdn-create-new-endpoint.md#create-a-new-cdn-endpoint).
   
9. Ha szeretné-e a profil mentése az irányítópulton való rögzítéséhez könnyebb keresni, jelölje be a létrehozásukat követően **rögzítés az irányítópulton**.
    
10. Válassza ki **létrehozása** a profil létrehozásához. 

