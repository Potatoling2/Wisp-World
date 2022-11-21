/**
 * Java text based rpg.
 *
 * @author Potatoling2
 * @version Alpha 2.0
 * future stuff:
 * bosses
 * enemy descriptions
 * shops and items
 * game classes (rpg)   
 * saving system that can ask for levels and change starting accordingly
 * gacha system
 * gui
 * FEEDBACK TAKEAWAYS:
 * make classes (can be improved but has been implemented)
 * spacing between outputs 
 * mana for bosses, special ai, special attacks, etc
 */
//stuff i need to make code work, these are packages not included with base java so you need to import them
import java.util.Scanner;
import java.util.Random;
import java.util.LinkedList;
import java.lang.Math;
public class Game {
    //integer setup, these values are actually to reset them so atk, def, etc can't stack and make game break
    static int playerhp;
    static int playeratk;
    static int playerdef;
    static int playermp;
    static int enemyatk;
    static int turncount = 0;
    //keeps track of game stuff, these are the base values for skills and their mana costs
    static String[] skills = {"Power Slash", "Heal", "Blade of Fury"};
    static int[] costs = {5, 3, 15};
    //game variables
    static int playerloc = 0;
    static int money = 0;
    static int textspeed = 15;
    static Scanner Input = new Scanner(System.in);
    //keeps track of player victories for each area
    static int[] bosscount = {0, 0, 0, 0, 0, 0, 0};
    static int[] staticbossreq = {10, 10, 20, 25, 100, 100, 100};
    static int[] bossreq = {10, 10, 20, 25, 100, 100, 100};
    static boolean[] danger = {false, false, false, false, false, false, false};
    //flags to reduce bugs
    static boolean isdedge = false;
    static boolean nobattle = false;
    static boolean perfguard = false;
    static boolean aggromsg = false;
    static boolean hit = false;
    static boolean inn = false;
    static boolean bossbattle = false;
    //random function so i dont need to type this 400 times
    public static int Rand(int min, int max){
        return (int)(Math.random() * (max - min + 1)) + min;
    }
    public static void wait(int ms) {
        try
        {
            Thread.sleep(ms);
        }
        catch(InterruptedException ex)
        {
            Thread.currentThread().interrupt();
        }
    }
    public static void scroll(String a) {
        int length = a.length();
        LinkedList<Character> newdialogue = new LinkedList<Character>();
        for(int b = 0; b < length; b++) {
            newdialogue.add(a.charAt(b));
        }
        for(int c = 0; c < length; c++) {
            System.out.print(newdialogue.get(c));
            wait(textspeed);
        }
        System.out.println("");
    }
    //exp method
    public static void Exp(int playerexp, int enemyexp, int expreq, int level) {
        Player.exp = Player.exp + Enemy.exp;
        scroll(Player.name + " gained " + Enemy.exp + " exp!");
        if(Player.exp >= Player.expreq) {
            Player.level++;
            scroll("Level Up!");
            scroll("");
            scroll(Player.name + " is now Level " + Player.level + "!");
            Player.exp = Player.exp - Player.expreq;
            //exp formula
            double expround = Math.pow(1.15,Player.level - 1) * 100;
            Player.expreq = (int)Math.round(expround);
            StatGains();
        }
        scroll("Exp is: " + Player.exp + "/" + Player.expreq);
        scroll("");
    }
    //statgains method
    public static void StatGains() {
        int hpgain = 6;
        int atkgain = 2;
        int defgain = 1;
        int mpgain = 2;
        scroll("");
        Player.hp = playerhp;
        Player.hp = Player.hp + hpgain;
        playerhp = Player.hp;
        scroll("HP increased by " + hpgain + "!");
        Player.atk = Player.atk + atkgain;
        playeratk = Player.atk;
        scroll("Atk increased by " + atkgain + "!");
        if(Player.level % 3 == 0) {
            Player.def = Player.def + defgain;
            playerdef = Player.def;
            scroll("Def increased by " + defgain + "!");
        }
        Player.mp = playermp;
        Player.mp = Player.mp + mpgain;
        playermp = Player.mp;
        scroll("MP increased by " + mpgain + "!");
    }
    //big baller shotcaller method
    public static int Money(int enemymoney, int money) {
        money = money + enemymoney;
        if(enemymoney > 0) {
            scroll("Gained " + enemymoney + " coins.");
            scroll("");
        }
        else if(enemymoney < 0 && inn) {
            enemymoney = enemymoney * -1;
            scroll("Paid " + enemymoney + " coins.");
            inn = false;
        }
        else if(enemymoney < 0 && !inn) {
            enemymoney = enemymoney * -1;
            scroll("Lost " + enemymoney + " coins.");
        }
        return(money);
    }
    //battle method, where the fighting takes place, gets called with whatever the enemy and player stats are at the current time
    public static void Battle() {
        //resetting values to their defaults, once again to prevent stacking
        scroll("");
        scroll(Player.name + "'s HP is " + Player.hp + ". " + Enemy.name + "'s HP is " + Enemy.hp + ".");
        //loops battle sequence until enemy or player runs out of hp
        while(Player.hp > 0 && Enemy.hp > 0) {
            scroll("Press 1 for attack, 2 for guard, 3 for skills.");
            scroll("Press enter after inputting a NUMBER (NOT ANYTHING ELSE) to proceed.");
            turncount++;
            enemyatk = Enemy.atk;
            if(turncount > 1) {
                Player.def = playerdef;
            }
            int aggrothresh = Enemy.hp/4;
            //restores mana every turn
            if(Player.mp < playermp) {
                Player.mp = Player.mp + Player.mpregen;
            }
            //makes it so mana can't overflow
            if(Player.mp > playermp) {
                Player.mp = playermp;
            }
            //asks for user's input so they can choose their action
            int input = Input.nextInt();
            if(input == 1) {
                //player attack, enemyhp is set equal to that value
                Enemy.hp = attack(Player.hp, Enemy.hp, Player.atk, Enemy.def, Player.mp, Player.acc, Player.name, Enemy.name);
                //if you win it breaks the loop so enemy won't attack when it's already dead
                if(Enemy.hp <= 0) {
                    scroll("You Won!");
                    Exp(Player.exp, Enemy.exp, Player.expreq, Player.level);
                    money = Money(Enemy.money, money);
                    bosscount[playerloc]++;
                    if(bossbattle) {
                        bossreq[playerloc] = bossreq[playerloc] + staticbossreq[playerloc];
                        bossbattle = false;
                    }
                    break;
                }
            }
            //guard method, increases defense
            else if(input == 2) {
                Player.def = guard(Player.def);
            }
            //skills menu
            else if(input == 3) {
                scroll("");
                scroll("Select a skill.");
                scroll("Press 1 for " + skills[0] + ", 2 for " + skills[1] + ", 3 for " + skills[2] + ", etc.");
                scroll("Mana Costs are: "); 
                //prints costs of all skills using for loop
                for(int i = 0; i < skills.length; i++) {
                    scroll((i + 1) + ": " + costs[i]);
                }
                //takes input on which skill to use
                int skillinput = Input.nextInt();
                //will only work if enough mana
                if(Player.mp > costs[skillinput - 1]) {
                    if(skillinput == 1) {
                        //modifies attack for that action only
                        Player.atk = Player.atk + Player.atk/2;
                        Player.mp = Player.mp - costs[0];
                        Enemy.hp = attack(Player.hp, Enemy.hp, Player.atk, Enemy.def, Player.mp, Player.acc, Player.name, Enemy.name);
                        Player.atk = playeratk;
                        if(Enemy.hp <= 0) {
                            scroll("You Won!");
                            Exp(Player.exp, Enemy.exp, Player.expreq, Player.level);
                            money = Money(Enemy.money, money);
                            bosscount[playerloc]++;
                            if(bossbattle) {
                                bossreq[playerloc] = bossreq[playerloc] + staticbossreq[playerloc];
                                bossbattle = false;
                            } 
                            break;
                        }
                    }
                    else if(skillinput == 2) {
                        //heals % of max hp
                        int healamt = Player.hp/5;
                        Player.hp = Player.hp + healamt;
                        if(Player.hp > playerhp) {
                            Player.hp = playerhp;
                        }
                        Player.mp = Player.mp - costs[1];
                    }
                    else if(skillinput == 3) {
                        //essentially an ultimate
                        Player.atk = Player.atk * 2;
                        Player.mp = Player.mp - costs[2];
                        Enemy.hp = attack(Player.hp, Enemy.hp, Player.atk, Enemy.def, Player.mp, Player.acc, Player.name, Enemy.name);
                        Player.atk = playeratk;
                        if(Enemy.hp <= 0) {
                            scroll("You won!");
                            Exp(Player.exp, Enemy.exp, Player.expreq, Player.level);
                            money = Money(Enemy.money, money);
                            bosscount[playerloc]++;
                            if(bossbattle) {
                                bossreq[playerloc] = bossreq[playerloc] + staticbossreq[playerloc];
                                bossbattle = false;
                            }
                            break;
                        }
                    }
                }
                else if(Player.mp < costs[skillinput - 1]) {
                    scroll("Not enough mana.");
                    scroll("");
                }
                else {
                    //error case if user puts in a bad input
                    scroll("Invalid action.");
                    scroll("");
                }
            }
            else if(input == 4) {
                scroll("dev info:");
                scroll("Turncount is " + turncount + ".");
                scroll("Player mp is " + Player.mp + ".");
                scroll("OgPlayermp is " + playermp + ".");
                scroll("Mpregen is " + Player.mpregen + ".");
                scroll("OgPlayerhp is " + playerhp + ".");
                scroll("Playerhp is " + Player.hp + ".");
                scroll("OgPlayeratk is " + Player.atk + ".");
                scroll("Playeratk is " + playeratk + ".");
                scroll("OgPlayerdef is " + Player.def + ".");
                scroll("Playerdef is " + playerdef + ".");
                scroll("OgEnemyatk is " + Enemy.atk + ".");
                scroll("Enemyatk is " + enemyatk + ".");
                scroll("EnemyCount for current area is " + bosscount[playerloc] + ".");
                scroll("");
            }
            else {
                //another error case
                scroll("Invalid action.");
                scroll("");
            }
            if(Enemy.hp > aggrothresh) {
                //standard enemy attack if they're above the threshhold to go into berserk mode
                Player.hp = enemyattack(Player.hp, Enemy.hp, Enemy.atk, Player.def, Player.mp, Enemy.acc, Player.name, Enemy.name);
                if(Player.hp <= 0) {
                    //if enemy kills player
                    scroll(Player.name + " was slain. Restart to continue.");
                    isdedge = true;
                    break;
                }
            }
            else {
                if(Enemy.aggro == 0) {
                    //when it gets to this part, the atk will be multiplied by 2 for the turn, but will reset after
                    Enemy.atk = Enemy.atk * 2;
                }
                if(!aggromsg && Enemy.aggro == 0) {
                    //will only display the message once
                    scroll(Enemy.name + " is growing desperate. Attacks are now far more severe!");
                    scroll("");
                    aggromsg = true;
                }
                //for if below aggrothreshhold but i don't want enemy to aggro
                Player.hp = enemyattack(Player.hp, Enemy.hp, Enemy.atk, Player.def, Player.mp, Enemy.acc, Player.name, Enemy.name);
                Enemy.atk = enemyatk;
                //WORK HERE enemy attack fixes
                if(Player.hp <= 0) {
                    scroll(Player.name + " was slain. Restart to continue.");
                    isdedge = true;
                    break;
                }
            }
        }
    }
    public static int attack(int a, int b, int c, int d, int e, int f, String g, String h) {
        //this is where player attacks with damage calculator so it saves some time
        int playerdmg = dmgcalc(c, d, f);
        b = b - playerdmg;
        //prevents negative hp from appearing
        if(b < 0) {
            b = 0;
        }
        scroll("");
        scroll(Enemy.name + " took " + playerdmg + " damage!");
        //flag for message
        if(!hit) {
            scroll(Player.name + " Missed!");
            scroll("");
        }
        scroll("");
        scroll(g + "'s HP is " + a + ". " + h + "'s HP is " + b + ".");
        scroll("MP is now " + e + ".");
        return b;
    }
    public static int guard(int a) {
        //calls random method and generates defense between 0 and 5
        int def = Rand(0,5);
        if(def == 5) {
            //if defense rolled to be 5, perfect guard will nullify damage for the turn
            def = 999;
            scroll("Perfect Guard! Damage will be fully nullified this turn!");
            perfguard = true;
        }
        else {
            perfguard = false;
        }
        //adds that value to base defense to determine total defense
        a = a + def;
        if(def <= a + 5) {
            scroll("Defense up!");
        }
        return a;
    }
    public static int enemyattack(int a, int b, int c, int d, int e, int f, String g, String h) {
        //same as player attack method but for enemies
        int enemydmg = dmgcalc(c, d, f);
        a = a - enemydmg;
        if(a < 0) {
            a = 0;
        }
        scroll("");
        scroll(Player.name + " took " + enemydmg + " damage!");
        //flag for message
        if(!hit && !perfguard) {
            scroll(Enemy.name + " Missed!");
            scroll("");
        }
        scroll("");
        scroll(g + "'s HP is " + a + ". " + h + "'s HP is " + b + ".");
        scroll("MP is now " + e + ".");
        return a;
    }
    public static int dmgcalc(int a, int b, int c) {
        //damage calculator to add randomness to attack value
        int dmgmin = -1 * a/5;
        int dmgmax = a/5;
        int dmgroll = Rand(dmgmin,dmgmax);
        a = a + dmgroll;
        //accuracy check
        int hitcheck = Rand(0,99);
        if(hitcheck >= c) {
            a = 0;
            hit = false;
        }
        else {
            hit = true;
        }
        //damage takes defense into account
        int dmg = a - b;
        //prevents negative damage
        if(dmg < 0) {
            dmg = 0;
        }
        return dmg;
    }
    public static void Navigation(String a) {
        //method for determining what enemy will spawn based on player choice
        int enemymin = 0;
        int enemymax = 99;
        int enemyrand = Rand(0,99);
        nobattle = false;
        if(a.toUpperCase().contains("PLAINS")) {
            if(playerloc == 0){
                scroll("You choose to stay in the Vernier plains to keep fighting.");
                scroll("");
            }
            else {
                //updates player position and gives info
                playerloc = 0;
                scroll("This is the Vernier Plains. A large area perfect for new champions to begin their journeys.");
                scroll("Danger Level: 1");
                if(!danger[playerloc]) {
                    scroll("Do you want to continue? Press 1 for yes. Anything else for no.");
                    int cont = Input.nextInt();
                    if(cont != 1) {
                        scroll("You decide to turn back.");
                        nobattle = true;
                    }
                    danger[playerloc] = true;
                }
                scroll("");
            }
            if(bosscount[playerloc] >= bossreq[playerloc] && !nobattle) {
                scroll("A powerful foe lies ahead. Do you wish to challenge them? Press 1 for yes. Anything else for no.");
                int confirmation = Input.nextInt();
                if(confirmation == 1) {
                    Enemy LesserDrake = new Enemy();
                    LesserDrake.Attributes(100, 20, 5, 79, 0, 250, 250, "Lesser Drake");
                    scroll("You stumble upon the lair of a lesser drake that terrorizes the Vernier Plains.");
                    scroll("Defeat it for a nice bounty of rewards.");
                    bossbattle = true;
                }
                else {
                    scroll("You decide to turn back.");
                    scroll("");
                }
            }
            //generates a random enemy to fight, updates attributes of enemy for battle
            if(enemyrand >= 0 && enemyrand < 33 && !nobattle && !bossbattle) {
                Enemy Slime = new Enemy();
                Slime.Attributes(30, 5, 0, 79, 1, 20, 15, "Slime");
                scroll("A small slime blocks the path!");
            }
            else if(enemyrand >= 33 && enemyrand < 67 && !nobattle && !bossbattle) {
                Enemy Rat = new Enemy();
                Rat.Attributes(40, 3, 1, 79, 1, 20, 20, "Large Rat");
                scroll("A large rat bares it's teeth at you.");
            }
            else if(enemyrand >= 67 && enemyrand < 100 && !nobattle && !bossbattle) {
                Enemy Goblin = new Enemy();
                Goblin.Attributes(25, 8, 0, 84, 0, 25, 25, "Goblin");
                scroll("A goblin approaches menacingly!");
            }
        }
        else if(a.toUpperCase().contains("FOREST")) {
            //same stuff different enemies
            if(playerloc == 1){
                scroll("You choose to stay in the Whisperwoods to keep fighting.");
                scroll("");
            }
            else {
                playerloc = 1;
                scroll("This is the Whisperwoods. A dark forest that's sure to be infested with insects and creatures.");
                scroll("Danger Level: 3");
                if(!danger[playerloc]) {
                    scroll("Do you want to continue? Press 1 for yes. Anything else for no.");
                    int cont = Input.nextInt();
                    if(cont != 1) {
                        scroll("You decide to turn back.");
                        nobattle = true;
                    }
                    danger[playerloc] = true;
                }
                scroll("");
            }
            if(bosscount[playerloc] >= bossreq[playerloc] && !nobattle) {
                scroll("A powerful foe lies ahead. Do you wish to challenge them? 1 for yes, 2 for no.");
                int confirmation = Input.nextInt();
                if(confirmation == 1) {
                    Enemy OrcChief = new Enemy();
                    OrcChief.Attributes(500, 20, 8, 64, 0, 800, 1000, "Orc Chieftan");
                    scroll("You've found your way to the Orc's main tribal home.");
                    scroll("The Orc Chieftan challenges you to a one on one battle.");
                    scroll("Defeat him for a nice bounty of rewards.");
                    bossbattle = true;
                }
                else {
                    scroll("You decide to turn back.");
                    scroll("");
                }
            }
            if(enemyrand >= 0 && enemyrand < 50 && !nobattle && !bossbattle) {
                Enemy Spider = new Enemy();
                Spider.Attributes(35, 10, 0, 89, 1, 40, 20, "Spider");
                scroll("A spider crawls from above!");
            }
            else if(enemyrand >= 50 && enemyrand < 70 && !nobattle && !bossbattle) {
                Enemy Orc = new Enemy();
                Orc.Attributes(50, 7, 2, 69, 0, 70, 40, "Orc");
                scroll("You stumble across an orc in the woods. It's angry!");
                scroll("It attacks you in rage!");
            }
            else if(enemyrand >= 70 && enemyrand < 100 && !nobattle && !bossbattle) {
                Enemy Ent = new Enemy();
                Ent.Attributes(45, 8, 1, 79, 1, 50, 25, "Ent");
                scroll("You brush the branch of a tree...");
                scroll("It's actually an ent!");
            }
        }
        else if(a.toUpperCase().contains("DESERT")) {
            //same stuff different enemies
            if(playerloc == 2){
                scroll("You choose to stay in the Shambhala Desert to keep fighting.");
                scroll("");
            }
            else {
                playerloc = 2;
                scroll("This is the Shambhala Desert. An expansive wasteland full of monsters and bandits alike.");
                scroll("Danger Level: 7");
                if(!danger[playerloc]) {
                    scroll("Do you want to continue? Press 1 for yes. Anything else for no.");
                    int cont = Input.nextInt();
                    if(cont != 1) {
                        scroll("You decide to turn back.");
                        nobattle = true;
                    }
                    danger[playerloc] = true;
                }
                scroll("");
            }
            if(bosscount[playerloc] >= bossreq[playerloc] && !nobattle) {
                scroll("A powerful foe lies ahead. Do you wish to challenge them? Press 1 for yes. Anything else for no.");
                int confirmation = Input.nextInt();
                if(confirmation == 1) {
                    Enemy Sphinx = new Enemy();
                    Sphinx.Attributes(777, 80, 8, 99, 0, 2500, 777, "The Sphinx");
                    scroll("The wise Sphinx roams these lands. If you enter her territory, she will surely attack.");
                    scroll("She smells something tasty and readies herself to attack!");
                    scroll("Defeat her for a nice bounty of rewards.");
                    bossbattle = true;
                }
                else {
                    scroll("You decide to turn back.");
                    scroll("");
                }
            }
            if(enemyrand >= 0 && enemyrand < 35 && !nobattle && !bossbattle) {
                Enemy Wyrm = new Enemy();
                Wyrm.Attributes(80, 18, 1, 89, 0, 75, 50, "Sand Wyrm");
                scroll("The wyrm attacks from below!");
            }
            else if(enemyrand >= 35 && enemyrand < 61 && !nobattle && !bossbattle) {
                Enemy Snake = new Enemy();
                Snake.Attributes(100, 14, 0, 79, 1, 80, 50, "Rattlesnake");
                scroll("A snake crawls forth.");
            }
            else if(enemyrand >= 61 && enemyrand < 81 && !nobattle && !bossbattle) {
                Enemy Bandit = new Enemy();
                Bandit.Attributes(85, 16, 2, 94, 0, 80, 100, "Bandit");
                scroll("A bandit runs by to take your money!");
                money = Money(-1 * Enemy.money, money);
                scroll("The bandit pulls out a knife to take the rest!");
            }
            else if(enemyrand >= 81 && enemyrand < 100 && !nobattle && !bossbattle) {
                Enemy Cacturne = new Enemy();
                Cacturne.Attributes(120, 10, 3, 84, 1, 100, 40, "Cacturne");
                scroll("The cactus you were looking at opens it eyes!");
            }
        }
        else if(a.toUpperCase().contains("CASTLE")) {
            //same stuff different enemies
            if(playerloc == 3){
                scroll("You choose to stay in the Blackthorn Castle to keep fighting.");
                scroll("");
            }
            else {
                playerloc = 3   ;
                scroll("This is the Blackthorn Castle. Home of the Knight-King Darius. His legions of minions await you here.");
                scroll("Danger Level: 10");
                if(!danger[playerloc]) {
                    scroll("Do you want to continue? Press 1 for yes. Anything else for no.");
                    int cont = Input.nextInt();
                    if(cont != 1) {
                        scroll("You decide to turn back.");
                        nobattle = true;
                    }
                    danger[playerloc] = true;
                }
                scroll("");
            }
            if(bosscount[playerloc] >= bossreq[playerloc] && !nobattle) {
                scroll("A powerful foe lies ahead. Do you wish to challenge them? Press 1 for yes. Anything else for no.");
                int confirmation = Input.nextInt();
                if(confirmation == 1) {
                    Enemy theKing = new Enemy();
                    theKing.Attributes(1000, 150, 15, 89, 0, 50000, 10000, "King Darius");
                    scroll("You have reached the throne room of the Knight-King Darius himself.");
                    scroll("He will challenge you to a duel if you approach.");
                    scroll("Defeat him for a nice bounty of rewards.");
                    scroll("WARNING: This foe is especially dangerous.");
                    bossbattle = true;
                }
                else {
                    scroll("You decide to turn back.");
                    scroll("");
                }
            }
            if(enemyrand >= 0 && enemyrand < 30 && !nobattle && !bossbattle) {
                Enemy Legionnare = new Enemy();
                Legionnare.Attributes(100, 20, 2, 84, 0, 100, 80, "Legionnare");
                scroll("The legion sends forward one to duel.");
            }
            else if(enemyrand >= 30 && enemyrand < 60 && !nobattle && !bossbattle) {
                Enemy Mage = new Enemy();
                Mage.Attributes(90, 25, 0, 84, 1, 123, 75, "Castle Mage");
                scroll("You have disturbed the mage's study.");
                scroll("They move to unleash their fury upon you!");
            }
            else if(enemyrand >= 60 && enemyrand < 80 && !nobattle && !bossbattle) {
                Enemy Archer = new Enemy();
                Archer.Attributes(80, 30, 1, 94, 1, 110, 100, "Shadow Archer");
                scroll("An arrow whizzes by your head from behind!");
                scroll("The Archer takes aim!");
            }
            else if(enemyrand >= 80 && enemyrand < 100 && !nobattle && !bossbattle) {
                Enemy KGuard = new Enemy();
                KGuard.Attributes(125, 30, 3, 79, 0, 150, 150, "King's Guard");
                scroll("The King has sent one of his personal warriors to take you down.");
            }
        }
        else if(a.toUpperCase().contains("OCEAN")) {
            scroll("It would be unwise to head for this area yet, Champion.");
            nobattle = true;
        }
        else if(a.toUpperCase().contains("UNDERWORLD")) {
            scroll("It would be unwise to head for this area yet, Champion.");
            nobattle = true;
        }
        else if(a.toUpperCase().contains(".ETC")) {
            scroll("̶̬̱̰͠S̶̬̱̰͠p̴͇̔̅̃e̵̢̥̿c̴̩̳͊ì̴̡̨̟̠̀͛̎a̶͚̠̪̽͝l̸̙̦̈́́̚ ̴͌̈́͐̃͜C̵͔̤̏̇̈́ͅȧ̶̖͔̫̓͜s̷̝͒e̷̼͒̅̈́͘ ̴̼͝ͅĚ̴̪͎͕̐̚r̶͙̜͖͑ȓ̸̹̈́o̸̯̍̀͑̂r̸̢̥͈̣̊̒̿̀");
            nobattle = true;
        }
        else if(a.toUpperCase().contains("STATS")) {
            //show stats method
            scroll("");
            scroll(Player.name + "'s Stats are:");
            scroll("Level: " + Player.level);
            scroll("HP: " + Player.hp + "/" + playerhp);
            scroll("Atk: " + Player.atk);
            scroll("Def: " + Player.def);
            scroll("MP: " + Player.mp + "/" + playermp);
            scroll("To next level: " + Player.exp + "/" + Player.expreq);
            scroll("Coins: " + money);
            nobattle = true;
        }
        else if(a.toUpperCase().contains("INN")) {
            int innprice = Player.level * 10;
            int trueinnprice = innprice * -1;
            scroll("Welcome to the inn!");
            scroll("Would you like to stay the night?");
            scroll("It's " + innprice + " coins.");
            scroll("Press 1 for yes, 2 for no.");
            int pay = Input.nextInt();
            if(pay == 1) {
                inn = true;
                Money(trueinnprice, money);
                scroll("Thanks for your patronage!");
                Player.hp = playerhp;
                scroll("Your HP was maxed out.");
                scroll("");
            }
            else {
                scroll("Oh well, see you next time!");
                scroll("");
            }
            nobattle = true;
        }
        else { 
            //error case for nonvalid area name
            scroll("Invalid Area Name, try again.");
            scroll("");
            nobattle = true;
        }
    }
    public static void main(String args[]) {
        
        //game starts here
        scroll("Welcome to Wisp World. You are a wisp, a small spirit that can take control of lifeless husks called champions.");
        scroll("Each champion has it's own unique set of skills, and usually have a 'class' denoting their powers.");
        scroll("Before we begin, would you like to change any options? Yes/No");
        String choice = Input.nextLine();
        if(choice.toUpperCase().equals("YES")) {
            scroll("Select your preferred text speed: Slow/Medium/Fast/Instant");
            scroll("");
            String textchoice = Input.nextLine();
            if(textchoice.toUpperCase().equals("SLOW")) {
                textspeed = 30;
                scroll("Speed changed to Slow.");
            }
            else if(textchoice.toUpperCase().equals("MEDIUM")) {
                textspeed = 15;
                scroll("Speed changed to Medium.");
            }
            else if(textchoice.toUpperCase().equals("FAST")) {
                textspeed = 5;
                scroll("Speed changed to Fast.");
            }
            else if(textchoice.toUpperCase().equals("INSTANT")) {
                textspeed = 0;
                scroll("Speed changed to Instant.");
            }
            else {
                scroll("Invalid speed.");
            }
        }
        else {
            scroll("Alright then, let's proceed.");
        }
        scroll("You are in the Vernier Plains. One of these champions, a warrior can be seen.");
        scroll("You head over to inhabit the warrior as your own.");
        scroll("What is the warrior's name?");
        String playername = Input.nextLine();
        if(playername.equals("Schmekle")) {
            scroll("S̶̬̱̰͠p̴͇̔̅̃e̵̢̥̿c̴̩̳͊ì̴̡̨̟̠̀͛̎a̶͚̠̪̽͝l̸̙̦̈́́̚ ̴͌̈́͐̃͜C̵͔̤̏̇̈́ͅȧ̶̖͔̫̓͜s̷̝͒e̷̼͒̅̈́͘ ̴̼͝ͅĚ̴̪͎͕̐̚r̶͙̜͖͑ȓ̸̹̈́o̸̯̍̀͑̂r̸̢̥͈̣̊̒̿̀");
            System.exit(0);
        }
        scroll("And it's host? (Your own name)");
        String hostname = Input.nextLine();
        if(hostname.equals("Schmekle")) {
            scroll("S̶̬̱̰͠p̴͇̔̅̃e̵̢̥̿c̴̩̳͊ì̴̡̨̟̠̀͛̎a̶͚̠̪̽͝l̸̙̦̈́́̚ ̴͌̈́͐̃͜C̵͔̤̏̇̈́ͅȧ̶̖͔̫̓͜s̷̝͒e̷̼͒̅̈́͘ ̴̼͝ͅĚ̴̪͎͕̐̚r̶͙̜͖͑ȓ̸̹̈́o̸̯̍̀͑̂r̸̢̥͈̣̊̒̿̀");
            System.exit(0);
        }
        //generates player stats, will change for different classes later
        Player.Stats(30, 10, 0, 20, 2, 94, 0, 100, 1, playername);
        //generates first enemy, might turn into a tutorial type enemy later
        Enemy Slime = new Enemy();
        Slime.Attributes(20, 5, 0, 79, 1, 50, 10, "Baby Slime");
        scroll("");
        scroll("Wonderful. The legend of " + Player.name + " has begun.");
        scroll("");
        scroll("There is a small " + Enemy.name + " ahead.");
        playerhp = Player.hp;
        playeratk = Player.atk;
        playerdef = Player.def;
        playermp = Player.mp;
        enemyatk = Enemy.atk;
        //first battle
        Battle();
        //keeps asking if you would like to battle until game confirms you died
        while(!isdedge) {
            scroll("Where should " + Player.name + " head next?");
            scroll("Input words of where you'd like to go.");
            scroll("Example keywords: Plains, Desert, Castle, Forest, etc.");
            scroll("If you would like to check stats, type 'Stats'.");
            scroll("If you would like to use the inn to heal, type 'Inn'.");
            scroll("If the tale of Champion " + Player.name + " is over, type 'Whisper'.");
            String loc = Input.nextLine();
            //ends game early so i dont have to die to test things
            if(loc.toUpperCase().contains("WHISPER")) {
                scroll("Thank you for playing!");
                break;
            }
            //puts user input string through navigation method, if it matches to one of the cases, itll do that
            Navigation(loc);
            //makes it so that battles dont occur if they mess up input or other special case
            if(!nobattle) {
                turncount = 0;
                Battle();
            }
        }
    }
}
