공장 타이쿤입니다.

지식이 많이 없이 만든거라 많이 어설픈 상태입니다.


개인적으로 코드를 분석하는것도 괜찮다고 생각합니다.


굉장히 맛있어요.(진짜요)


코드는 수정중입니다!!

아래는 코드입니다.
```#include <stdio.h>
#include <stdlib.h>
#include <windows.h>
#include <time.h>

enum { TREE, STONE, IRON, GOLD, DIAMOND, RESOURCE_COUNT };

const char* resource_name[RESOURCE_COUNT] =
{ "나무", "돌", "철", "금", "다이아몬드" };

int resource[RESOURCE_COUNT]={0};
int processed[RESOURCE_COUNT]={0};
int price[RESOURCE_COUNT]={10,20,50,150,300};

int collect_speed[RESOURCE_COUNT]={3000,3000,3000,3000,3000};
int process_speed[RESOURCE_COUNT]={5000,5000,5000,5000,5000};

int money=0;

int auto_collect=0;
int auto_process=0;

int collect_upgrade_cost=300;
int process_upgrade_cost=400;

int gambling_unlocked=0;
int gambling_profit=0;

void clear_input(){ while(getchar()!='\n'); }

void progress_bar(const char* msg,int ms){
    int step=ms/20;
    for(int i=0;i<=20;i++){
        system("cls");
        printf("%s\n[",msg);
        for(int j=0;j<20;j++) printf(j<i?"#":" ");
        printf("] %d%%\n",i*5);
        Sleep(step);
    }
}

void show_status(){
    printf("돈: %d원\n",money);
    printf("----------------------\n");
    for(int i=0;i<RESOURCE_COUNT;i++){
        if(resource[i]>0 || processed[i]>0){
            printf("%s:%d",resource_name[i],resource[i]);
            if(processed[i]>0)
                printf("  가공%s:%d",resource_name[i],processed[i]);
            printf("\n");
        }
    }
    printf("----------------------\n");
}

void gather(){
    int sel;
    system("cls");
    printf("채집할 자원 선택\n");
    for(int i=0;i<RESOURCE_COUNT;i++)
        printf("%d. %s\n",i+1,resource_name[i]);
    printf("선택: ");
    scanf("%d",&sel); clear_input();
    sel--;

    if(sel<0||sel>=RESOURCE_COUNT) return;

    progress_bar("채집 중...",collect_speed[sel]);
    auto_process ? processed[sel]++ : resource[sel]++;
}

void process(){
    if(auto_process) return;
    int sel;
    system("cls");
    printf("가공할 자원 선택\n");
    for(int i=0;i<RESOURCE_COUNT;i++)
        if(resource[i]>0)
            printf("%d. %s\n",i+1,resource_name[i]);
    printf("선택: ");
    scanf("%d",&sel); clear_input();
    sel--;

    if(sel<0||sel>=RESOURCE_COUNT||resource[sel]==0) return;

    progress_bar("가공 중...",process_speed[sel]);
    resource[sel]--;
    processed[sel]++;
}

void sell_all(){
    int sum=0;
    for(int i=0;i<RESOURCE_COUNT;i++){
        sum+=resource[i]*price[i];
        sum+=processed[i]*price[i]*2;
        resource[i]=processed[i]=0;
    }
    money+=sum;
}

void gambling(){
    int bet;
    system("cls");
    printf("===== 도박 =====\n");
    printf("최소 배팅: 1000원\n");
    printf("현재 돈: %d원\n",money);
    printf("배팅 금액 입력 (0 취소): ");
    scanf("%d",&bet); clear_input();

    if(bet==0 || bet<1000 || bet>money) return;

    money-=bet;

    int slot[3];
    for(int i=0;i<3;i++)
        slot[i]=(rand()%3==0)?1:(rand()%2?3:7);

    printf("\n결과: [%d] [%d] [%d]\n",slot[0],slot[1],slot[2]);

    int mul=0;
    if(slot[0]==slot[1]&&slot[1]==slot[2]){
        if(slot[0]==1) mul=10;
        else if(slot[0]==3) mul=50;
        else mul=100;
    }
    else if(slot[0]==slot[1]||slot[1]==slot[2]||slot[0]==slot[2]){
        int n=(slot[0]==slot[1])?slot[0]:
              (slot[1]==slot[2])?slot[1]:slot[0];
        if(n==1) mul=0.5;
        else if(n==3) mul=1;
        else mul=3;
    }

    int gain=(int)(bet*mul);
    money+=gain;
    gambling_profit+=gain-bet;

    printf("획득 금액: %d원\n",gain);
    system("pause");
}

int main(){
    srand(time(NULL));
    int menu;

    while(1){
        system("cls");
        show_status();

        printf("1. 자원 채집\n");
        printf("2. 자원 가공\n");
        printf("3. 전체 판매\n");
        printf("4. 상점\n");
        if(gambling_unlocked) printf("6. 도박\n");
        printf("0. 종료\n");
        printf("선택: ");

        scanf("%d",&menu);
        clear_input();

        if(menu==777){
            gambling_unlocked=1;
            continue;
        }

        if(menu==1) gather();
        else if(menu==2) process();
        else if(menu==3) sell_all();
        else if(menu==6 && gambling_unlocked) gambling();
        else if(menu==0) return 0;
    }
}

```
