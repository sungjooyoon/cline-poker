---
title: "cline-poker"
author: "Sungjoo Yoon"
date: "2024-09-29"
output: html_document
---



```{}
The following code is for accessibly simulating poker bots in an object-oriented environment

import java.util.ArrayList;
import java.util.Scanner;
// lines 1-2 utilize the Java utilities ArrayList and Scanner

public class ClinePokerInterface
{
   // main arguments (used to start / call a new game)
   public static void main(String[] args)
   {
      startGame();
   }
   
   // data - card class
   public static class Card
   {
      private String suit, rank;
      private int statOne;
      
      // initialize indiviual statistics for card
      public Card(String r, String s)
      {
         suit = s;
         rank = r;
         cardEmpirics();
      }
      
      // procedure - card initializer
      private void cardEmpirics()
      {    
         String[] ranks = {"0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A"};
         int i = 0;
         while (i < ranks.length)
         {
            if(ranks[i].equals(rank))
            {
               statOne = i;
               break;
            }  
            i++;
         }
      }
      
      // procedure - call card's statistic
      public int getStatistic()
      {
         return statOne;
      }
      
      // procedure - call card's suit
      public String getSuit()
      {
         return suit;
      }
      
      // procedure - call card's rank
      public String getRank()
      {
         return rank;
      }
      
      // procedure - display card's rank and suit
      public String toString()
      {
         return "(" + rank + suit + ")";
      }
      
      // procedure - is the statistic equally comparative?
      public boolean equals(Object obj)
      {
         return statOne == ((Card)obj).getStatistic();
      }
   }
   
   // data - game class
   public static class ClinePoker
   {
      // this initializes four lists for data abstraction: 
      // 2 'ArrayLists' for the deck and hand
      // 2 'Arrays' for suits / ranks (card data) stored w/ default values
      // the first 2 will be utilized later as generic, adaptable data sets w/ no initial values
      // the second 2 will be utilized as a traversable data set for Card-class methods
      // for the purposes of this question, we can examine ArrayList<Card> hand
      private ArrayList<Card> deck;
      private ArrayList<Card> hand;
      private static final String[] suits = {"C", "D", "H", "S"};
      private static final String[] ranks = {"2", "3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A"};
         
      // data - class protection
      // the public ClinePoker() method then initializes the deck and hand lists
      // it also calls fillDeck() to fill the deck ArrayList
      public ClinePoker()
      {
         deck = new ArrayList<Card>();
         hand = new ArrayList<Card>();
         fillDeck();
      }
      
      // procedure - fill new draw
      // this is the mechanism through which individual cards (x5) are utilized to fill the hand list
      private void drawCards(int num)
      {
         for (int i = 0; i < num; i++)
         {
            int randomizer = (int) (Math.random() * deck.size() - 1) + 1;
            String pullRanks = deck.get(randomizer).getRank();
            String pullSuits = deck.get(randomizer).getSuit();
            deck.remove(randomizer);
            Card drawAdd = new Card(pullRanks, pullSuits);
            hand.add(drawAdd);
         }
         sortHand();
      }
      
      // procedure - fill new hand
      public void createHand()
      {
         drawCards(5);
         sortHand();
      }
      
      // data - assign deck's values
      private void fillDeck()
      {
         for (int i = 0; i < 12; i++)
         {
            for (int e = 0; e < 4; e++)
            {
               Card filler = new Card(ranks[i], suits[e]);
               deck.add(filler);
            }
         }
      }
      
      // procedure - clear hand and deck, refill deck
      public void resetDeck()
      {
         deck.clear();
         hand.clear();
         fillDeck();
      }
      
      // procedure - display current hand
      public void printHand()
      {
         String truncate = "";
         for (int i = 0; i < hand.size(); i++)
         {
            truncate = truncate + hand.get(i) + " ";
         }
         System.out.println("Current hand: " + truncate);
      }
      
      // procedure - drop cards, redraw in game
      // this procedure is my highlighted response to 3C
      // the parameter is (int numCards), with a void return type
      public void dropAndDraw(int numCards)
      {
         ArrayList<Integer> replace = new ArrayList<Integer>();
         Scanner input = new Scanner(System.in);
         System.out.println("What indexes do you wish to discard?");
         if ((numCards >= 0) && (numCards <= 5))
         {
            for(int i = 0; i < numCards; i++)
            {
               int num = input.nextInt();
               replace.add(num);
            }
            dropAndDrawMech(replace);
         }
         else
            System.out.println("Sorry, that is not a valid index. Your original draw will be kept.");
      }
      
      // this is a procedure called within my answer to 3C
      // data - traversing mechanism for discarding and drawing
      // this sub-procedure will assist method dropAndDraw
      // it utilizes the inputted values in said method to replace w/ new draw
      private void dropAndDrawMech(ArrayList<Integer> indexes)
      {
         for (int i = indexes.size(); i > 0; i--)
         {
            int removeIndex = indexes.get(i - 1);
            hand.remove(removeIndex);
         }
         drawCards(indexes.size());
         sortHand();
      }
      
      // procedure - sort data by statistics
      private void sortHand()
      {
         for(int i = 1; i < hand.size(); i++)
         {
            Card before = hand.get(i);
            int next = before.getStatistic();
            int j = i;
            while (j > 0 && hand.get(j - 1).getStatistic() > next)
            {
               hand.set(j, hand.get(j - 1));
               j--;
            }
            hand.set(j, before);
         }
      }
      
      // procedure - initialize hand + cards, then sort
      public ClinePoker(ArrayList<Card> cards)
      {
         hand = cards;
         sortHand();
      }
      
      // procedure - combinatorics, winning hand of a Single Pair
      public boolean singlePair()
      {
         int tracker = 0;
         for (int i = 0; i < hand.size() - 1; i++)
            if (hand.get(i).getRank().equals(hand.get(i + 1).getRank()))
               tracker = tracker + 1;
         return tracker == 1;
      }
      
      // procedure - combinatorics, winning hand of a Double Pair        
      public boolean doublePair()
      {
         String front = hand.get(0).getRank();
         String mid = hand.get(2).getRank();
         String back = hand.get(4).getRank();
         int frontInt = 0, midInt = 0, backInt = 0;
         for (int i = 0; i < hand.size(); i++)
         {
            if (front.equals(hand.get(i).getRank()))
               frontInt = frontInt + 1;
            else if (mid.equals(hand.get(i).getRank()))
               midInt = midInt + 1;
            else if (back.equals(hand.get(i).getRank()))
               backInt = backInt + 1;
         }
         if ((frontInt==2 && midInt==2) || (midInt==2 && backInt==2) || (frontInt==2 && backInt==2))
            return true;
         return false;
      }
      
      // procedure - combinatorics, winning hand of 3 of a Kind
      public boolean threeKind()
      {
         String front = hand.get(0).getRank();
         String mid = hand.get(2).getRank();
         String back = hand.get(4).getRank();
         int frontInt = 0, midInt = 0, backInt = 0;
         for (int i = 0; i < hand.size(); i++)
         {
            if (front.equals(hand.get(i).getRank()))
               frontInt = frontInt + 1;
            else if (mid.equals(hand.get(i).getRank()))
               midInt = midInt + 1;
            else if (back.equals(hand.get(i).getRank()))
               backInt = backInt + 1;
         }
         if (frontInt==3 || midInt==3 || backInt==3)
            return true;
         return false;
      }  
      
      // procedure - combinatorics, winning hand of a Straight
      public boolean regularStraight()
      {
         boolean straight = true;
         for (int i = 1; i < hand.size(); i++)
         {
            if (!(hand.get(i).getStatistic() == hand.get(i - 1).getStatistic() + 1))
               return false;
         }
         return straight;
      }
      
      // procedure - combinatorics, winning hand of a Flush   
      public boolean regularFlush()
      {
         String baseline = hand.get(0).getSuit();
         for (int i = 0; i < hand.size(); i++)
         {
            if (!(baseline == hand.get(i).getSuit()))
               return false;
         }
         return true;
      }
      
      // procedure - combinatorics, winning hand of a Full House
      public boolean fullHouse()
      {
         String front = hand.get(0).getRank();
         String back = hand.get(4).getRank();
         int frontInt = 0, backInt = 0;
         for (int i = 0; i < hand.size(); i++)
         {
            if (front.equals(hand.get(i).getRank()))
               frontInt = frontInt + 1;
            if (back.equals(hand.get(i).getRank()))
               backInt = backInt + 1;
         }
         if ((frontInt==2 && backInt==3) || (frontInt==3 && backInt==2))
            return true;
         return false;
      }

      // procedure - combinatorics, winning hand of 4 of a Kind
      public boolean fourKind()
      {
         String front = hand.get(0).getRank();
         String back = hand.get(4).getRank();
         int frontInt = 0, backInt = 0;
         for (int i = 0; i < hand.size(); i++)
         {
            if (front.equals(hand.get(i).getRank()))
               frontInt = frontInt + 1;
            if (back.equals(hand.get(i).getRank()))
               backInt = backInt + 1;
         }
         if ((frontInt==1 && backInt==4) || (frontInt==4 && backInt==1))
            return true;
         return false;
      }  

      // procedure - combinatorics, winning hand of Straight Flush
      public boolean straightFlush()
      {
         return (regularFlush() == true && regularStraight() == true);
      }

      // procedure - combinatorics, winning hand of Royal Flush
      // traverses the list "hand" to check to see if the hand's cards are a Royal Flush
      public boolean royalFlush()
      {
         boolean rf = false;
         int ten = 0, j = 0, q = 0, k = 0, a = 0;
         for (Card c : hand)
         {
            if (c.getRank().equals("10"))
               ten = ten + 1;
            else if (c.getRank().equals("J"))
               j = j + 1;
            else if (c.getRank().equals("Q"))
               q = q + 1;
            else if (c.getRank().equals("K"))
               k = k + 1;
            else if (c.getRank().equals("A"))
               a = a + 1;
         }
         if (ten==1 && j==1 && q==1 && k==1 && a==1)
           rf = true;
         return (regularFlush() == true && rf == true);
      }
            
      // procedure - output code
      public void printWinnings()
      {
         if (royalFlush())
            System.out.println("Royal Flush. You win enough simoleons to be Jeffrey Bezos!");
         else if (straightFlush())
            System.out.println("Straight Flush. You win enough simoleons to quit your job!");
         else if (fourKind())
            System.out.println("4-of-a-Kind. You win a good chunk of simoleons!");
         else if (fullHouse())
            System.out.println("Full House. You win enough simoleons for a lobster dinner!");
         else if (regularFlush())
            System.out.println("Flush. You win enough simoleons to buy a steak dinner!");
         else if (threeKind())
            System.out.println("3-of-a-Kind. You win some simoleons.");
         else if (doublePair())
            System.out.println("Two-Pair. You win a very modest amount of simoleons.");
         else if (singlePair())
            System.out.println("Pair. You win a modicum of simoleons.");
         else
            System.out.println("No matches. Sorry, you lost.");
      }
   }
   
   // procedure - initialize UX, and the entire game
   public static void startGame()
   {
      Scanner input = new Scanner(System.in);
      ClinePoker game = new ClinePoker();
      boolean playAgain = true;
      while(playAgain)
      {
         game.createHand();
         System.out.println("Howdy! Welcome to the mystical world of Cline Poker!" + "\n");
         System.out.print("The rules are simple." + "\n");
         System.out.print("1) The game is 5-card-draw, single-discard style." + "\n");
         System.out.print("2) No antes or blinds, this is single-player." + "\n");
         System.out.println("3) Winning hands: Flush (any kind), Full House, Straight, 3-or-4-of-a-kind, and pairs." + "\n");
         game.printHand();
         System.out.print("How many cards would you like to draw? ");
         int numCards = input.nextInt();
         System.out.println("");
         game.dropAndDraw(numCards);
         game.printHand();
         game.printWinnings();
         System.out.print("\nPlay again? ");
         String yesOrNo = input.next();
         if(!yesOrNo.equalsIgnoreCase("yes"))
            playAgain = false;
         else
         {
            game.resetDeck();
            System.out.println("\n---------------------------\n");
         }
      }
      System.out.println("\n---------------------------\n");
      System.out.println("Thank you for playing Cline Poker!");
   }
}
```

Pages index.html generator script


```r
knit("index.Rmd", output = "index.md")
```

```
## 
## 
## processing file: index.Rmd
```

```
## Error in parse_block(g[-1], g[1], params.src, markdown_mode): Duplicate chunk label 'setup', which has been used for the chunk:
## knitr::opts_chunk$set(echo = TRUE)
## library(markdown)
## library(knitr)
```

```r
markdownToHTML("index.md", "index.html")
```
