공장 타이쿤입니다.

대충 만든거라 많이 어설픈 상태입니다.

코드는 아직 수정중입니다!!

아래는 코드입니다.
```#include <stdio.h>
#include <stdlib.h>
#include <windows.h>
#include <time.h>

// ------------------ 자원 보유량 ------------------
int tree = 0, stone = 0, iron = 0, gold = 0, diamond = 0;         // 채집 자원
int p_tree = 0, p_stone = 0, p_iron = 0, p_gold = 0, p_diamond = 0; // 가공 자원

// ------------------ 자원 가격 ------------------
int price_tree = 10, price_stone = 20, price_iron = 50, price_gold = 150, price_diamond = 300;

// ------------------ 돈 ------------------
int money = 0;

// ------------------ 자원 발견 레벨 ------------------
int resource_level = 1; // 1=나무만, 2=돌, 3=철, 4=금, 5=다이아몬드

// ------------------ 채집 속도(ms) ------------------
int collect_speed_tree = 2000, collect_speed_stone = 4000, collect_speed_iron = 6000, collect_speed_gold = 8000, collect_speed_diamond = 10000;

// ------------------ 가공 속도(ms) ------------------
int process_speed_tree = 2000, process_speed_stone = 3000, process_speed_iron = 4000, process_speed_gold = 5000, process_speed_diamond = 6000;

// ------------------ 도박 활성화 ------------------
int gambling_unlocked = 0;

// ------------------ 함수 선언 ------------------
void show_resources();
void gather_resource();
void process_resource();
void sell_menu();
void shop();
void gambling();

// ------------------ 메인 ------------------
int main() {
    int menu;
    srand((unsigned)time(NULL));

    while(1) {
        system("cls");
        printf("돈: %d원\n", money);

        // 보유 자원 표시
        show_resources();

        printf("\n1. 자원 채집\n");
        printf("2. 자원 가공\n");
        printf("3. 전체 판매\n");
        printf("4. 업그레이드 상점\n");
        if(gambling_unlocked) printf("5. 도박\n");
        printf("0. 종료\n");
        printf("선택: ");
        scanf("%d", &menu);

        // 치트 코드 적용
        if(menu == 777) {
            gambling_unlocked = 1;
            printf("도박 시스템이 활성화되었습니다!\n");
            Sleep(1500);
            continue;
        }
        if(menu == 805) {
            money += 100000;
            printf("돈 100,000원을 지급받았습니다!\n");
            Sleep(1500);
            continue;
        }

        switch(menu) {
            case 1: gather_resource(); break;
            case 2: process_resource(); break;
            case 3: sell_menu(); break;
            case 4: shop(); break;
            case 5: if(gambling_unlocked) gambling(); break;
            case 0: return 0;
            default:
                printf("잘못된 입력\n");
                Sleep(1000);
        }
    }
}

// ------------------ 자원 표시 ------------------
void show_resources() {
    int shown = 0;

    if(tree > 0) { printf("나무: %d  ", tree); shown=1; }
    if(p_tree > 0) { printf("가공 나무: %d  ", p_tree); shown=1; }
    if(stone > 0) { printf("돌: %d  ", stone); shown=1; }
    if(p_stone > 0) { printf("가공 돌: %d  ", p_stone); shown=1; }
    if(iron > 0) { printf("철: %d  ", iron); shown=1; }
    if(p_iron > 0) { printf("가공 철: %d  ", p_iron); shown=1; }
    if(gold > 0) { printf("금: %d  ", gold); shown=1; }
    if(p_gold > 0) { printf("가공 금: %d  ", p_gold); shown=1; }
    if(diamond > 0) { printf("다이아몬드: %d  ", diamond); shown=1; }
    if(p_diamond > 0) { printf("가공 다이아몬드: %d  ", p_diamond); shown=1; }

    if(shown) printf("\n");
}

// ------------------ 자원 채집 ------------------
void gather_resource() {
    int collect_time = 0;
    char res_name[20] = "나무";

    switch(resource_level) {
        case 1: collect_time = collect_speed_tree; sprintf(res_name,"나무"); break;
        case 2: collect_time = collect_speed_stone; sprintf(res_name,"돌"); break;
        case 3: collect_time = collect_speed_iron; sprintf(res_name,"철"); break;
        case 4: collect_time = collect_speed_gold; sprintf(res_name,"금"); break;
        case 5: collect_time = collect_speed_diamond; sprintf(res_name,"다이아몬드"); break;
    }

    printf("%s 채집 중...\n", res_name);
    Sleep(collect_time);

    switch(resource_level) {
        case 1: tree++; printf("%s 채집 완료! 현재 나무: %d\n", res_name, tree); break;
        case 2: stone++; printf("%s 채집 완료! 현재 돌: %d\n", res_name, stone); break;
        case 3: iron++; printf("%s 채집 완료! 현재 철: %d\n", res_name, iron); break;
        case 4: gold++; printf("%s 채집 완료! 현재 금: %d\n", res_name, gold); break;
        case 5: diamond++; printf("%s 채집 완료! 현재 다이아몬드: %d\n", res_name, diamond); break;
    }

    Sleep(1000);
}

// ------------------ 자원 가공 ------------------
void process_resource() {
    int process_time = 0;
    char res_name[20] = "나무";

    if(tree==0 && stone==0 && iron==0 && gold==0 && diamond==0) {
        printf("가공할 자원이 없습니다.\n");
        Sleep(1000);
        return;
    }

    if(tree>0) { process_time = process_speed_tree; sprintf(res_name,"나무"); }
    else if(stone>0) { process_time = process_speed_stone; sprintf(res_name,"돌"); }
    else if(iron>0) { process_time = process_speed_iron; sprintf(res_name,"철"); }
    else if(gold>0) { process_time = process_speed_gold; sprintf(res_name,"금"); }
    else if(diamond>0) { process_time = process_speed_diamond; sprintf(res_name,"다이아몬드"); }

    printf("%s 가공 중...\n", res_name);
    Sleep(process_time);

    if(tree>0) { tree--; p_tree++; printf("%s 가공 완료! 가공 나무: %d\n", res_name, p_tree); }
    else if(stone>0) { stone--; p_stone++; printf("%s 가공 완료! 가공 돌: %d\n", res_name, p_stone); }
    else if(iron>0) { iron--; p_iron++; printf("%s 가공 완료! 가공 철: %d\n", res_name, p_iron); }
    else if(gold>0) { gold--; p_gold++; printf("%s 가공 완료! 가공 금: %d\n", res_name, p_gold); }
    else if(diamond>0) { diamond--; p_diamond++; printf("%s 가공 완료! 가공 다이아몬드: %d\n", res_name, p_diamond); }

    Sleep(1000);
}

// ------------------ 전체 판매 ------------------
void sell_menu() {
    int total = 0;

    total += tree*price_tree; total += p_tree*price_tree*2;
    total += stone*price_stone; total += p_stone*price_stone*2;
    total += iron*price_iron; total += p_iron*price_iron*2;
    total += gold*price_gold; total += p_gold*price_gold*2;
    total += diamond*price_diamond; total += p_diamond*price_diamond*2;

    printf("전체 판매 완료! 획득 돈: %d원\n", total);

    money += total;

    tree = stone = iron = gold = diamond = 0;
    p_tree = p_stone = p_iron = p_gold = p_diamond = 0;

    Sleep(1500);
}

// ------------------ 상점 ------------------
void shop() {
    int sel;
    system("cls");

    static int collect_level = 0;
    static int process_level = 0;
    static int resource_cost_level = 0;
    static int value_level = 0; // 자원 가치 업그레이드 단계

    int collect_cost = 200 + collect_level*100;
    int process_cost = 300 + process_level*150;
    int resource_cost = 500 + resource_cost_level*200;
    int value_cost = 400 + value_level*200; // 자원 가치 업그레이드 가격

    printf("===== 업그레이드 상점 =====\n");
    printf("1. 채집 속도 업그레이드 (가격: %d원)\n", collect_cost);
    printf("2. 가공 속도 업그레이드 (가격: %d원)\n", process_cost);
    if(resource_level<5)
        printf("3. 자원 발견 (현재 레벨 %d, 가격: %d원)\n", resource_level, resource_cost);
    printf("4. 자원 가치 업그레이드 (가격: %d원)\n", value_cost);
    printf("0. 뒤로가기\n");
    printf("선택: ");
    scanf("%d", &sel);

    if(sel==1 && money>=collect_cost) {
        money -= collect_cost;
        int old_tree = collect_speed_tree;
        int old_stone = collect_speed_stone;
        int old_iron = collect_speed_iron;
        int old_gold = collect_speed_gold;
        int old_diamond = collect_speed_diamond;

        collect_level++;
        collect_speed_tree = (collect_speed_tree>500)?collect_speed_tree-200:collect_speed_tree;
        collect_speed_stone = (collect_speed_stone>500)?collect_speed_stone-200:collect_speed_stone;
        collect_speed_iron = (collect_speed_iron>500)?collect_speed_iron-200:collect_speed_iron;
        collect_speed_gold = (collect_speed_gold>500)?collect_speed_gold-200:collect_speed_gold;
        collect_speed_diamond = (collect_speed_diamond>500)?collect_speed_diamond-200:collect_speed_diamond;

        printf("채집 속도 업그레이드 완료!\n");
        printf("나무: %d -> %d ms\n", old_tree, collect_speed_tree);
        printf("돌: %d -> %d ms\n", old_stone, collect_speed_stone);
        printf("철: %d -> %d ms\n", old_iron, collect_speed_iron);
        printf("금: %d -> %d ms\n", old_gold, collect_speed_gold);
        printf("다이아몬드: %d -> %d ms\n", old_diamond, collect_speed_diamond);
    } else if(sel==2 && money>=process_cost) {
        money -= process_cost;
        int old_tree = process_speed_tree;
        int old_stone = process_speed_stone;
        int old_iron = process_speed_iron;
        int old_gold = process_speed_gold;
        int old_diamond = process_speed_diamond;

        process_level++;
        process_speed_tree = (process_speed_tree>500)?process_speed_tree-200:process_speed_tree;
        process_speed_stone = (process_speed_stone>500)?process_speed_stone-200:process_speed_stone;
        process_speed_iron = (process_speed_iron>500)?process_speed_iron-200:process_speed_iron;
        process_speed_gold = (process_speed_gold>500)?process_speed_gold-200:process_speed_gold;
        process_speed_diamond = (process_speed_diamond>500)?process_speed_diamond-200:process_speed_diamond;

        printf("가공 속도 업그레이드 완료!\n");
        printf("나무: %d -> %d ms\n", old_tree, process_speed_tree);
        printf("돌: %d -> %d ms\n", old_stone, process_speed_stone);
        printf("철: %d -> %d ms\n", old_iron, process_speed_iron);
        printf("금: %d -> %d ms\n", old_gold, process_speed_gold);
        printf("다이아몬드: %d -> %d ms\n", old_diamond, process_speed_diamond);
    } else if(sel==3 && money>=resource_cost && resource_level<5) {
        money -= resource_cost;
        resource_level++;
        resource_cost_level++;
        printf("새로운 자원을 발견했습니다! 이제 ");
        switch(resource_level) {
            case 2: printf("돌"); break;
            case 3: printf("철"); break;
            case 4: printf("금"); break;
            case 5: printf("다이아몬드"); break;
        }
        printf("도 채집 가능합니다.\n");
    } else if(sel==4 && money>=value_cost) {
        money -= value_cost;
        value_level++;
        price_tree += 10;
        price_stone += 30;
        price_iron += 60;
        price_gold += 100;
        price_diamond += 200;
        printf("자원 가치 업그레이드 완료! 새로운 판매 가격 적용\n");
    } else if(sel!=0) {
        printf("돈이 부족하거나 조건이 맞지 않습니다.\n");
    }

    Sleep(1500);
}

// ------------------ 도박 ------------------
void gambling() {
    int bet;
    printf("베팅 금액을 입력하세요 (현재 돈: %d원): ", money);
    scanf("%d", &bet);
    if(bet <= 0 || bet > money) {
        printf("잘못된 금액입니다.\n");
        Sleep(1000);
        return;
    }

    money -= bet;

    int slot[3];
    for(int i=0;i<3;i++) {
        int r = rand() % 3;
        if(r==0) slot[i]=1;
        else if(r==1) slot[i]=3;
        else slot[i]=7;
    }

    printf("슬롯 결과: | %d | %d | %d |\n", slot[0], slot[1], slot[2]);

    int count1=0, count3=0, count7=0;
    for(int i=0;i<3;i++) {
        if(slot[i]==1) count1++;
        if(slot[i]==3) count3++;
        if(slot[i]==7) count7++;
    }

    double multiplier = 0.5;
    if(count1==2) multiplier=1;
    else if(count3==2) multiplier=2;
    else if(count7==2) multiplier=3;
    else if(count1==3) multiplier=5;
    else if(count3==3) multiplier=10;
    else if(count7==3) multiplier=137;

    int win = (int)(bet*multiplier);
    money += win;

    printf("배당률: %.1fx, 획득 돈: %d원\n", multiplier, win);
    Sleep(2000);
}
```
