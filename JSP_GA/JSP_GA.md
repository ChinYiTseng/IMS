# IMS: Job Shop Scheduling Problem #
*POLab*
<br>
*2017/09/2X*

[【Homepage】](https://github.com/PO-LAB/Intelligent-Manufacturing-Systems/blob/master/README.md)

## Job Shop Scheduling Problem ##
After we got the basic concepts of the Genetic Algorithm (GA), we might desire to solve different applications using GA.
<br/>
<br/>
✿ If we want to review the Genetic Algorithm, please check [HERE](https://github.com/PO-LAB/Intelligent-Manufacturing-Systems/blob/master/Genetic_Algorithm/Genetic_Algorithm.md)!
<br/>
<br/>
In this article, we will introduce how to implement the genetic algorithm on scheduling problem. We use a job shop problem (JSP) as an example. In the job shop problem, multiple jobs are processed on several machines. Each job consists of several operations (tasks) with predefined sequence, that is, operations (tasks) must be performed in a given order and on a specific machine. The problem solution indicates the ideal jobs which are assigned to machines at particular times. Common scheduling objective is to minimize the maximum length of the schedule which also called makespan. Of course, we can consider other objectives such as minimize tardy jobs, minimize total weighted earliness and tardiness (TWET), and so on.
<br/>
<br/>
Below is the simple example of JSP. There are four jobs (J1-J4) and four machines (M1-M4). There exist certain fixed routes (sequence of machines) which may not be the same for each job, as Figure 1 shows. Then, we can reorganize the routes and get the Table 1 which shows the machines sequence. And we may also know the processing time dataset, as shown in the Table 2. At last, we can follow the rules of JSP and find out feasible solutions. In Figure 2, the Gantt Chart is used to show our result.

<div align=center>
<img src="https://github.com/ChinYiTseng/IMS/blob/master/JSP_GA/JSP_simplecase.png" alt="GitHub" width="614" height="356"/>
</div>
<br/>

So now let's learn and try to implement the GA on the 15×15 Job Shop Scheduling Problem! 

## Example: 15×15 Job Shop Scheduling Problem ##
This example is a job shop scheduling problem from E. Taillard (1993).<br/>
This test is also known as TA01 in the literature, and its optimal makespan is known to be 1,231 (E.D. Taillard, 1994). <br/><br/>
There are 15 jobs (J1-J15) and 15 machines (M1-M15).<br/>
Every job must be processed on each of the 15 machines in a predefined sequence (O1-O15).<br/>
Our objective is to minimize the completion time of the last job to be processed, known as the makespan. <br/><br/>
The dataset is given as follows:
<br/><br/>
▸ Processing Time 

 Time |  O1  |  O2  |  O3  |  O4  |  O5  |  O6  |  O7  |  O8  |  O9  |  O10 |  O11 |  O12 |  O13 |  O14 |  O15 |
 :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
  J1  |  94  |  66  |  10  |  53  |  26  |  15  |  65  |  82  |  10  |  27  |  93  |  92  |  96  |  70  |  83  |
  J2  |  74  |  31  |  88  |  51  |  57  |  78  |   8  |   7  |  91  |  79  |  18  |  51  |  18  |  99  |  33  |
  J3  |   4  |  82  |  40  |  86  |  50  |  54  |  21  |   6  |  54  |  68  |  82  |  20  |  39  |  35  |  68  |
  J4  |  73  |  23  |  30  |  30  |  53  |  94  |  58  |  93  |  32  |  91  |  30  |  56  |  27  |  92  |   9  |
  J5  |  78  |  23  |  21  |  60  |  36  |  29  |  95  |  99  |  79  |  76  |  93  |  42  |  52  |  42  |  96  |
  J6  |  29  |  61  |  88  |  70  |  16  |  31  |  65  |  83  |  78  |  26  |  50  |  87  |  62  |  14  |  30  |
  J7  |  18  |  75  |  20  |   4  |  91  |  68  |  19  |  54  |  85  |  73  |  43  |  24  |  37  |  87  |  66  |
  J8  |  32  |  52  |   9  |  49  |  61  |  35  |  99  |  62  |   6  |  62  |   7  |  80  |   3  |  57  |   7  |
  J9  |  85  |  30  |  96  |  91  |  13  |  87  |  82  |  83  |  78  |  56  |  85  |   8  |  66  |  88  |  15  |
  J10 |   5  |  59  |  30  |  60  |  41  |  17  |  66  |  89  |  78  |  88  |  69  |  45  |  82  |   6  |  13  |
  J11 |  90  |  27  |   1  |   8  |  91  |  80  |  89  |  49  |  32  |  28  |  90  |  93  |   6  |  35  |  73  |
  J12 |  47  |  43  |  75  |   8  |  51  |   3  |  84  |  34  |  28  |  60  |  69  |  45  |  67  |  58  |  87  |
  J13 |  65  |  62  |  97  |  20  |  31  |  33  |  33  |  77  |  50  |  80  |  48  |  90  |  75  |  96  |  44  |
  J14 |  28  |  21  |  51  |  75  |  17  |  89  |  59  |  56  |  63  |  18  |  17  |  30  |  16  |   7  |  35  |
  J15 |  57  |  16  |  42  |  34  |  37  |  26  |  68  |  73  |   5  |   8  |  12  |  87  |  83  |  20  |  97  |

▸ Machines Sequence

Machine |  O1  |  O2  |  O3  |  O4  |  O5  |  O6  |  O7  |  O8  |  O9  |  O10 |  O11 |  O12 |  O13 |  O14 |  O15 |
 :--:   | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: | :--: |
  J1    |   7  |  13  |   5  |   8  |   4  |   3  |  11  |  12  |   9  |  15  |  10  |  14  |    6 |    1 |    2 |
  J2    |   5  |   6  |   8  |  15  |  14  |   9  |  12  |  10  |   7  |  11  |   1  |   4  |   13 |    2 |    3 |
  J3    |   2  |   9  |  10  |  13  |   7  |  12  |  14  |   6  |   1  |   3  |   8  |  11  |    5 |    4 |   15 |
  J4    |   6  |   3  |  10  |   7  |  11  |   1  |  14  |   5  |   8  |  15  |  12  |   9  |   13 |    2 |    4 |
  J5    |   8  |   9  |   7  |  11  |   5  |  10  |   3  |  15  |  13  |   6  |   2  |  14  |   12 |    1 |    4 |
  J6    |   6  |   4  |  13  |  14  |  12  |   5  |  15  |   8  |   3  |   2  |  11  |   1  |   10 |    7 |    9 |
  J7    |  13  |   4  |   8  |   9  |  15  |   7  |   2  |  12  |   5  |   6  |   3  |  11  |    1 |   14 |   10 |
  J8    |  12  |   6  |   1  |   8  |  13  |  14  |  15  |   2  |   3  |   9  |   5  |   4  |   10 |    7 |   11 |
  J9    |  11  |  12  |   7  |  15  |   1  |   2  |   3  |   6  |  13  |   5  |   9  |   8  |   10 |   14 |    4 |
  J10   |   7  |  12  |  10  |   3  |   9  |   1  |  14  |   4  |  11  |   8  |   2  |  13  |   15 |    5 |    6 |
  J11   |   5  |   8  |  14  |   1  |   6  |  13  |   7  |   9  |  15  |  11  |   4  |   2  |   12 |   10 |    3 |
  J12   |   3  |  15  |   1  |  13  |   7  |  11  |   8  |   6  |   9  |  10  |  14  |   2  |    4 |   12 |    5 |
  J13   |   6  |   9  |  11  |   3  |   4  |   7  |  10  |   1  |  14  |   5  |   2  |  12  |   13 |    8 |   15 |
  J14   |   9  |  15  |   5  |  14  |   6  |   7  |  10  |   2  |  13  |   8  |  12  |  11  |    4 |    3 |    1 |
  J15   |  11  |   9  |  13  |   7  |   5  |   2  |  14  |  15  |  12  |   1  |   8  |   4  |    3 |   10 |    6 |
  
### Encoding and Decoding ###
For solving the JSP, we use operation-based representation to encode a schedule. The schedule is represented as a sequence of operations and each gene stand for one operation. For a *n*-job and *m*-machine problem,a chromosome contains *n*×*m* genes. Each job appears in the chromosome exactly *m* times. A simple explaination is as follow:

<div align=center>
<img src="https://github.com/ChinYiTseng/IMS/blob/master/JSP_GA/JSP_endode_decode.png" alt="GitHub", width="626" height="341"/>
</div>
<br/>

### Problem Definition and Parameters Setting ###
Input the information according to problem definition. <br/>
Here, we will read Microsoft Excel spreadsheet file ([download](https://github.com/ChinYiTseng/IMS/raw/master/JSP_GA/JSP_dataset.xlsx)) instead of directly giving a value into the code.
```matlab
% xlsread(filename,sheet,xlRange), use Excel range syntax in "xlRange".
j_num = xlsread('JSP_dataset.xlsx','Parameters','B2'); % Job Counts
ma_num = xlsread('JSP_dataset.xlsx','Parameters','B3'); % Machine Counts
PT = xlsread('JSP_dataset.xlsx','ProcessingTime','B2:P16'); % Processing Time
Ma = xlsread('JSP_dataset.xlsx','MachineSequence','B2:P16'); % Machine Sequence
```

Let user decide the populationsize, crossover rate, mutation rate and iteration times.
```matlab
population_size = input('Please input the size of population: ');  % Ask user to input the population size.
if isempty (population_size)  % Set the default value (it works if user doesn't input anything to population_size).
    population_size = 10;  % Default value for population_size is equal to 10
end

crossover_rate = input('Please input the Crossover Rate: ');
if isempty (crossover_rate) 
    crossover_rate = 0.8;
end

mutation_rate = input('Please input the Mutation Rate: '); 
if isempty (mutation_rate) 
    mutation_rate = 0.1;
end

Num_Iteration = input('Please input the Iteration Times: '); 
if isempty (Num_Iteration) 
    Num_Iteration = 1000;
end
```

### Executive Time ###
Calculate executive time.
```matlab
tic  % Start stopwatch timer
…
toc  % Read elapsed time from stopwatch
```

### Initialize the Solution ###
Generate initial population (solution).
```matlab
population_list = zeros(population_size, j_num*ma_num); % Record population (includes all chromosomes).

for i = 1:population_size 
    population_list(i,1:j_num*ma_num) = randperm(j_num*ma_num); % Random permutation of the integers from 1 to j_num*ma_num inclusive.
    for j = 1:j_num*ma_num % cluster each gene as one operation. For example, k*ma_num < gene value < (k+1)*ma_num, then this gene value is job (k+1).  
        for k = 0:(j_num-1)
            if population_list(i,j) > k*ma_num && population_list(i,j) <= (k+1)*ma_num
                population_list(i,j) = k+1;
            end
        end
    end
end
```

### Crossover ###
Here, pairs of parent solutions are selected for two-point crossover, which calls for two points to be selected on the parent chromosomes. Everything between the two points is swapped between the chormosomes, rendering two offspring chromosomes.
```matlab
S = randperm(population_size); % Decide pairs of parent chromosomes.

for m = 1:(population_size/2)
    crossover_prob = rand(); % Generate the random probability.
    if (crossover_rate >= crossover_prob) % Crossover is happened only if crossover rate is larger than random probability.
        parent_1 = population_list(S(-1+2*m),1:j_num*ma_num); % Parent chromosome 1
        parent_2 = population_list(S(2*m),1:j_num*ma_num); % Parent chromosome 2
        
        child_1 = parent_1; % Record offspring chromosome 1
        child_2 = parent_2; % Record offspring chromosome 2
        
        cutpoint = randperm(j_num*ma_num); % Randomly choose two cut points.
        
        % two-point crossover
        cutpoint = sort(cutpoint(1:2)); % Sort the two cut points in ascending order.
        for k = cutpoint(1):cutpoint(2) 
            child_1(k) = parent_2(k);
            child_2(k) = parent_1(k);
        end
        
        % Save the results of crossover.
        population_list(S(-1+2*m),1:j_num*ma_num) = child_1;
        population_list(S(2*m),1:j_num*ma_num) = child_2;
    end
end
```

### Mutation ###
For mutaion, one gene in each chromosome alters if the probability is less than mutation rate. The gene which needs mutation is changed to another value. This may cause some jobs appear in the chromosome more than *m* times. Thus, we will have one step to repair it in the later. 

```matlab
for m = 1:population_size
    for j = 1:j_num*ma_num
        mutation_prob = rand();  % Generate the random probability.
        if mutation_rate >= mutation_prob  % Chromosomes mutate only if mutation rate is larger than random probability.
            ran_num = rand();  % Generate the random probability, and this probability will be clustered to one value which represents tha mutation result.
            for k = 0:(j_num-1)
                if ran_num > k*(1/j_num) && ran_num <= (k+1)*(1/j_num)
                    population_list(m,j) = k+1;
                end
            end
        end
    end
end
```

### Repairment ###
The crossover and mutation may break up the chromosome rules and give us one unfeasible solution, so repair our chromosomes is an important step. Here, we repair the chromosomes and let them become feasible solutions.

```matlab
for m = 1:population_size 
    % Calculate the job occurance counts in the chromosome.
    repair_or_not = zeros(1, j_num); % Record the occurance counts of each job.
    for j = 1:j_num*ma_num
        for k = 1:j_num
            if population_list(m,j) == k
                repair_or_not(k) = repair_or_not(k) + 1;
            end
        end
    end
    
    % Deal with the jobs with occurance counts larger than machine counts.
    for k = 1:j_num
        if repair_or_not(k) > ma_num
            r_ran_num = randperm(repair_or_not(k));  % Randomly choose certain number of genes at certain positions to be repaired.
            r_ran_num = sort(r_ran_num(1:(repair_or_not(k)-ma_num)));
            appeartime = 0;  
            for j = 1:j_num*ma_num
                if population_list(m,j) == k
                    appeartime = appeartime + 1;
                    for n = 1:(repair_or_not(k)-ma_num)
                        if appeartime == r_ran_num(n)
                            population_list(m,j) = 0;   % The selected gene will be substitued by 0.
                        end
                    end
                end
            end
        end
    end
    
    % Deal with the jobs with occurance counts less than machine counts.
    for k = 1:j_num 
        if repair_or_not(k) < ma_num
            zeroappeartime = 0;
            appeartime = 0;
            
            for j = 1:j_num*ma_num  % Calculate the occurance times of 0.
                if population_list(m,j) == 0
                    zeroappeartime = zeroappeartime + 1;
                end
            end
            
            r_ran_num = randperm(zeroappeartime);  % Randomly choose certain number of genes at certain positions to be repaired.
            r_ran_num = sort(r_ran_num(1:ma_num-(repair_or_not(k))));
            for j = 1:j_num*ma_num
                if population_list(m,j) == 0
                    appeartime = appeartime + 1;
                    for n = 1:(ma_num-repair_or_not(k))
                        if appeartime == r_ran_num(n)
                            population_list(m,j) = k;  % The selected gene will be substitued by the jobs value which occurance counts less than machine counts.
                        end
                    end
                end
            end
        end
    end
end
```
