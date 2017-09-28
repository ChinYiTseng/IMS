# IMS: Job Shop Scheduling Problem #
*POLab*
<br>
*2017/09/2X*

[【Homepage】](https://github.com/PO-LAB/Intelligent-Manufacturing-Systems/blob/master/README.md)

## Job Shop Scheduling Problem ##
After we got the basic concepts of Genetic Algorithm (GA), we might desire to solve different applications using GA.
<br/>
<br/>
✿ If we want to review the Genetic Algorithm, please check [HERE](https://github.com/PO-LAB/Intelligent-Manufacturing-Systems/blob/master/Genetic_Algorithm/Genetic_Algorithm.md)!
<br/>
<br/>
In this article, we will introduce how to implement the genetic algorithm on scheduling problem. We use a job shop problem (JSP) as an example. In job shop problem, multiple jobs are processed on several machines. Each job consists of several operations (tasks) with predefined sequence, that is, operations (tasks) must be performed in a given order and on a specific machine. The problem solution indicates the ideal jobs which are assigned to machines at particular times. Common scheduling objective is to minimize the maximum length of the schedule which also called makespan. Of course, we can consider other objectives such as minimize tardy jobs, minimize total weighted earliness and tardiness (TWET), and so on.

## Example: 15×15 Job Shop Scheduling Problem ##
This example is a job shop scheduling problem from E. Taillard (1993). This test is also known as TA01 in the literature, and its optimal makespan is known to be 1,231 (E.D. Taillard, 1994). <br/>
There are 15 jobs (J1-J15) and 15 machines (M1-M15). Every job must be processed on each of the 15 machines in a predefined sequence (O1-O15). Our objective is to minimize the completion time of the last job to be processed, known as the makespan. <br/><br/>
The dataset is given as follows:
<br/>
▸ Processing Time 

 Time |  M1  |  M2  |  M3  |  M4  |  M5  |  M6  |  M7  |  M8  |  M9  |  M10 |  M11 |  M12 |  M13 |  M14 |  M15 |
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
  J2    |    5  |   6  |   8  |  15  |  14  |   9  |  12  |  10  |   7  |  11  |   1  |   4  |   13 |    2 |    3 |
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
  
