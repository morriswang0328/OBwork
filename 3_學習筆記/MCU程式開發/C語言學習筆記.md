
### struct & pointer

```
#include <stdio.h>
#include <stdlib.h>

typedef struct
{
    int ws;
    struct
    {
        int y;    
    }x;
    
}Whltype;

Whltype frontWS;    // 變數宣告

void addWs(Whltype *whl)
{
    whl->ws += 2;
    whl->x.y += 2;
}

int main()
{
    /*** 指標變數宣告：
    1.要malloc指定出空間, 2.要放在main執行(不能放上面)
    ***/    
    Whltype *RearWS_p = malloc(sizeof(RearWS_p));
    
    frontWS.ws = 5;
    /*** 宣告變數後要變數指入，否則無效 ***/
    addWs(&frontWS);

    RearWS_p->ws = 10;

    //printf("Hello World");
    printf("%d, %d\n", frontWS.ws, frontWS.x.y );
    printf("%d, %d", RearWS_p->ws, RearWS_p->x.y );


    return 0;
}
```
