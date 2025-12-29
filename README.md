#include <stdio.h>
#include<stdlib.h>
#include<time.h>

void update_prices(float stock[], int count, int day)
{
    int j, sign;

    float* ptr = &stock[0];

    float random, max = 3 + (day*0.6);
    if (max > 15) max = 15;

    for(j = 0; j < count; j++)
    {   
        random = ((float)rand() / RAND_MAX) * max;
            
        sign = rand()%3 - 1;

        if(sign == 1)
        {
            *(ptr+j) += (*(ptr+j))*(random/100.0f);
        }
        else if(sign == -1)
        {
            (*(ptr+j)) -= (*(ptr+j))*(random/100.0f);
        }
    }
}

void print_prices(float stock[], char NAME[][20], int count)
{   
    int i;
    printf("Current market prices:\n");
    for(i = 0; i < count; i++)
    {
        printf("%d. %s = %.2f\n", i+1, NAME[i], stock[i]);
    }
}

void print_shares(int shares[] , char NAME[][20], int count)
{
    int i;
    printf("Your current holdings:\n");
    for(i = 0; i < count; i++)
    {
        printf("%d. %s = %d\n", i+1, NAME[i], shares[i]);
    }
}

void BUY(float stock[], float *ptr, int shares[])
{
    int index, qty;

    printf("Enter the stock number to buy: ");
    scanf("%d" , &index);

    if (index > 5 || index < 1)
    {
        printf("Invalid stock selection.\n");
        return;
    }
    
    printf("Enter quantity to purchase: ");
    scanf("%d" , &qty);
    if (qty < 0)
    {
        printf("Invalid quantity entered.\n");
        return;
    }
    

    if(*ptr >= stock[index-1]*qty)
    {
        *ptr -= qty*stock[index-1];
        shares[index-1] += qty;
    }
    else
    {
        printf("Insufficient funds for this transaction.\n");
        return;
    }

    printf("\n");
}

void SELL(float stock[], float *ptr, int shares[])
{
    int index, qty;

    printf("Enter the stock number to sell: ");
    scanf("%d" , &index);

    if (index > 5 || index < 1)
    {
        printf("Invalid stock selection.\n");
        return;
    }

    printf("Enter the quantity to sell: ");
    scanf("%d" , &qty);

    if (qty < 0)
    {
        printf("Invalid quantity entered.\n");
        return;
    }
    else if(qty <= shares[index-1])
    {
        shares[index-1] -= qty;
        *ptr += stock[index-1]*qty;
    }
    else
    {
        printf("Insufficient shares available.\n");
        return;
    }
    printf("\n\n");
}

void END(float *ptr)
{
    if (*ptr - 10000 > 0)
    {
        printf("Trading period complete. Total profit after 10 days: %.2f" , *ptr - 10000);
    }
    else if (*ptr - 10000 < 0)
    {
        printf("Trading period complete. Total loss after 10 days: %.2f" , 10000 - *ptr);
    }
    else
    {
        printf("Trading period complete. No net profit or loss after 10 days.");
    }
}

int main(){

    int day, shares[5] = {0, 0, 0, 0, 0}, option, i;

    float Stock[5] = {1000, 1500, 2000, 2500, 3000}, cash = 10000;
    float* ptr = &cash;

    char NAME[5][20] = {"NVIDIA" , "Pharma" , "TATA" , "Microsoft" , "Amazon"};

    srand(time(0));

    for ( day = 1; day <= 10; day++)
    {
        printf("------------------------- TRADING DAY %d -------------------------\n" , day);
        update_prices(Stock, 5, day);
        print_prices(Stock , NAME, 5);
        printf("\n");
        print_shares(shares, NAME, 5);

        do
        {
            printf("\nAvailable cash balance: %.2f\n\n", cash);
            printf("Select an action: -\n1. Buy shares.\n2. Sell shares.\n3. Close trading for the day.\nEnter Choice : ");
            scanf("%d" , &option);
            if(option == 1)
            {
                BUY(Stock, ptr, shares);
            }
            else if (option == 2)
            {
                SELL(Stock, ptr, shares);
            }
            else if (option == 3)
            {
                break;
            }
            else if ((option > 3) || (option < 1))
            {
                printf("Invalid selection. Please try again.");
            }
        }while (option != 3);
    }

    printf("\n");

    END(ptr);
    
    return 0;
}
