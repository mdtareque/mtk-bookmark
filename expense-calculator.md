

http://c2.com/doc/expense/

input 	output


	Jackson Hole Expenses
	    February, '81

Cal Diller ...

71.25  Cowboy Steak House
24  M/M
26  Mexican
60  Lift Tickets
13.50  Pizza
-------
CAL

Bill Pooley ...

48 * 3  Lift Tickets
14.04  Breakfast
368.16  Room
29.31  Drinks
-------
BILL

Ward Cunningham ...

14.73  Gas
14.2
13.86
17
14.5
13.76
-------
GAS  Total Gas

15  Blue Lion
48  Lift
30.55  Anthonys
69.24  Alpenhof
GAS  Total Gas
-------
WARD


Total Shared Expenses ...

CAL  Cal Diller
BILL  Bill Pooley
WARD  Ward Cunningham
-------
TOTAL / 3
-------
SHARE  Individual Share


Individual Balances ...

BILL  Bill's Expenses
SHARE DB
-------
SUM
3.86  Telephone, Cal
6.01  Telephone, Ward
-------
DB  Due Bill


CAL  Cal's Expenses
SHARE DB
-------
SUM
3.86 DB  Telephone
-------
DC  Due Cal


WARD  Ward's Expenses
SHARE DB
-------
SUM
6.01 DB  Telephone
-------
DW  Due Ward


Doublechecking ...

DB
DC
DW
-------
ERROR  Unaccounted Funds

	

	Jackson Hole Expenses
	    February, '81

Cal Diller ...

  71.25 Cowboy Steak House
  24.00 M/M
  26.00 Mexican
  60.00 Lift Tickets
  13.50 Pizza
-------
 194.75

Bill Pooley ...

  48.00 * 3 Lift Tickets
  14.04 Breakfast
 368.16 Room
  29.31 Drinks
-------
 555.51

Ward Cunningham ...

  14.73 Gas
  14.20
  13.86
  17.00
  14.50
  13.76
-------
  88.05 Total Gas

  15.00 Blue Lion
  48.00 Lift
  30.55 Anthonys
  69.24 Alpenhof
  88.05 Total Gas
-------
 250.84


Total Shared Expenses ...

 194.75 Cal Diller
 555.51 Bill Pooley
 250.84 Ward Cunningham
-------
1001.10 / 3
-------
 333.70 Individual Share


Individual Balances ...

 555.51 Bill's Expenses
 333.70 DB
-------
 221.81
   3.86 Telephone, Cal
   6.01 Telephone, Ward
-------
 231.68 Due Bill


 194.75 Cal's Expenses
 333.70 DB
-------
-138.95
   3.86 DB Telephone
-------
-142.81 Due Cal


 250.84 Ward's Expenses
 333.70 DB
-------
 -82.86
   6.01 DB Telephone
-------
 -88.87 Due Ward


Doublechecking ...

 231.68
-142.81
 -88.87
-------
   0.00 Unaccounted Funds

Here is the shell script that I used to do the calculations and write the report. It invokes AWK with the formatting program enclosed in quotes. The program keeps a running sum that it clears when it sees a blank line (NF==0). Other calculations are performed in place. The first occurrence of a variable name defines it as that sum. Subsequent occurrences become the stored value.
script

exec awk '
$1 ~ /^[A-Z]+[A-Z0-9]*$/ {
	if (sums[$1] == "" || $1 == "SUM") {
		sums[$1] = sum			# Define Symbol
		$1 = sum
		sum = 0
	}
	else {
		$1 = sums[$1]			# Dereference Symbol
	}
}

($1+0) != 0	{$1 = sprintf("%7.2f", $1)}	# Pretty Print
		{print}

$2 == "*"	{$1 *= $3}			# Explicit Calculations
$2 == "/"	{$1 /= $3}
$2 == "DB"	{$1 = -$1}
$2 == "CR"	{$1 = -$1}

NF == 0		{sum = 0}			# Implicit Summation
		{sum += $1}
' $1

