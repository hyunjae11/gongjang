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

const char* guide_text =
"이 게임은 자원을 채집하고 가공해 돈을 버는 게임입니다.\n"
"상점에서 자동화와 업그레이드를 해금할 수 있습니다.\n"
"엔딩 조건은 1,000,000원입니다.\n";

const char* achievement_desc[3] = {
    "모든 업그레이드 완료",
    "모든 자원과 가공 자원 20개 이상 보유",
    "도박으로 50000원 이상 수익"
};

int resource[RESOURCE_COUNT]={0};
int processed[RESOURCE_COUNT]={0};
int price[RESOURCE_COUNT]={10,20,50,150,300};

int collect_speed[RESOURCE_COUNT]={3000,3000,3000,3000,3000};
int process_speed[RESOURCE_COUNT]={2000,3000,4000,5000,6000};

int money=0;
int resource_level=1;

int auto_collect=0;
int auto_collect_level=1;
int auto_process=0;
int auto_sell=0;
int auto_sell_on=0;

int gamble_unlock=0;
int gamble_profit=0;

int collect_upgrade_cost=300;
int process_upgrade_cost=400;

void clear_input(){ while(getchar()!='\n'); }

void show_resources(){
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

void progress_bar(const char* title,int ms){
    int step=ms/20;
    for(int i=0;i<=20;i++){
        system("cls");
        printf("%s\n[",title);
        for(int j=0;j<20;j++) printf(j<i?"#":" ");
        printf("] %d%%\n",i*5);
        Sleep(step);
    }
}

int confirm_purchase(int need_money,int need_res[],int need_proc[]){
    system("cls");
    printf("===== 구매 확인 =====\n");
    printf("돈: %d / %d\n",money,need_money);

    for(int i=0;i<RESOURCE_COUNT;i++){
        if(need_res[i]>0)
            printf("%s: %d / %d\n",resource_name[i],resource[i],need_res[i]);
        if(need_proc[i]>0)
            printf("가공%s: %d / %d\n",resource_name[i],processed[i],need_proc[i]);
    }

    printf("\n1. 구매\n0. 취소\n");
    printf("선택: ");

    int sel;
    scanf("%d",&sel);
    clear_input();

    if(sel!=1) return 0;

    if(money<need_money){
        printf("돈이 부족합니다.\n");
        Sleep(1200);
        return 0;
    }

    for(int i=0;i<RESOURCE_COUNT;i++){
        if(resource[i]<need_res[i] || processed[i]<need_proc[i]){
            printf("자원이 부족합니다.\n");
            Sleep(1200);
            return 0;
        }
    }

    money-=need_money;
    for(int i=0;i<RESOURCE_COUNT;i++){
        resource[i]-=need_res[i];
        processed[i]-=need_proc[i];
    }
    return 1;
}

void gather(){
    int sel;
    printf("채집할 자원 선택\n");
    for(int i=0;i<resource_level;i++)
        printf("%d. %s\n",i+1,resource_name[i]);
    printf("선택: ");
    scanf("%d",&sel);
    clear_input();

    sel--;
    if(sel<0||sel>=resource_level) return;

    progress_bar("채집 중...",collect_speed[sel]);
    auto_process ? processed[sel]++ : resource[sel]++;
}

void process(){
    if(auto_process) return;
    int sel;
    printf("가공할 자원 선택\n");
    for(int i=0;i<RESOURCE_COUNT;i++)
        if(resource[i]>0)
            printf("%d. %s\n",i+1,resource_name[i]);
    printf("선택: ");
    scanf("%d",&sel);
    clear_input();

    sel--;
    if(sel<0||sel>=RESOURCE_COUNT||resource[sel]==0) return;

    progress_bar("가공 중...",process_speed[sel]);
    resource[sel]--; processed[sel]++;
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

void auto_collect_tick(){
    if(!auto_collect) return;
    int r=rand()%RESOURCE_COUNT;
    int base[5]={10,8,5,3,1};
    int gain=base[r]*auto_collect_level;
    auto_process ? (processed[r]+=gain):(resource[r]+=gain);
}

void shop(){
    int sel;
    system("cls");
    printf("===== 상점 =====\n");
    printf("1. 채집 속도 업그레이드\n");
    printf("2. 가공 속도 업그레이드\n");
    printf("3. 자동 채집 해금\n");
    printf("4. 자동 가공 해금\n");
    printf("0. 뒤로가기\n");
    printf("선택: ");

    scanf("%d",&sel);
    clear_input();

    int need_res[RESOURCE_COUNT]={0};
    int need_proc[RESOURCE_COUNT]={0};

    if(sel==1){
        if(collect_speed[0]<=1000){
            printf("이미 최대 속도입니다.\n");
            Sleep(1200); return;
        }
        if(confirm_purchase(collect_upgrade_cost,need_res,need_proc)){
            for(int i=0;i<RESOURCE_COUNT;i++) collect_speed[i]-=200;
            collect_upgrade_cost*=1.5f;
        }
    }
    else if(sel==2&&!auto_process){
        if(process_speed[0]<=3000){
            printf("이미 최대 속도입니다.\n");
            Sleep(1200); return;
        }
        if(confirm_purchase(process_upgrade_cost,need_res,need_proc)){
            for(int i=0;i<RESOURCE_COUNT;i++) process_speed[i]-=200;
            process_upgrade_cost*=1.5f;
        }
    }
    else if(sel==3&&!auto_collect){
        need_res[TREE]=need_proc[TREE]=10;
        need_res[STONE]=need_proc[STONE]=10;
        if(confirm_purchase(15000,need_res,need_proc))
            auto_collect=1;
    }
    else if(sel==4&&!auto_process){
        need_res[IRON]=need_proc[IRON]=10;
        need_res[GOLD]=need_proc[GOLD]=10;
        need_res[DIAMOND]=need_proc[DIAMOND]=10;
        if(confirm_purchase(50000,need_res,need_proc))
            auto_process=1;
    }
}

int main(){
    srand(time(NULL));
    int menu;

    while(1){
        system("cls");
        show_resources();

        printf("1. 자원 채집\n");
        printf("2. 자원 가공\n");
        printf("3. 전체 판매\n");
        printf("4. 상점\n");
        printf("5. 안내\n");
        printf("8. 엔딩\n");
        printf("0. 종료\n");
        printf("선택: ");

        scanf("%d",&menu);
        clear_input();

        auto_collect_tick();

        if(menu==1) gather();
        else if(menu==2) process();
        else if(menu==3) sell_all();
        else if(menu==4) shop();
        else if(menu==5){ system("cls"); printf("%s",guide_text); system("pause"); }
        else if(menu==8 && money>=1000000){
            system("cls");
            printf("엔딩을 달성했습니다.\n");
            system("pause");
            return 0;
        }
        else if(menu==0) return 0;
    }
}



```
