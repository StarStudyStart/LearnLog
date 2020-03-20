### TypeError: 'set' object is not reversible
    ```
    报错代码：
    urlpatterns = {
        path('home/', views.home, name='home'),
        path('market/', views.market, name='market'),
        path('mine/', views.mine, name='mine'),
        path('cart/', views.cart, name='cart'),
    }
    ```
    - 将urlpatterns = {}改为urlpatterns = []
        字典是无序的不能作为路由的数组