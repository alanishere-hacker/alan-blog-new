---
title: "Java键盘输入与循环递归编程实践——完全数、自幂数与二分查找"
published: 2024-10-15
description: "通过完全数判断、自幂数计算和递归二分查找三个编程任务，掌握Java键盘输入、循环控制与递归的基本编程方法。"
tags: ["Java", "循环", "递归", "二分查找", "完全数", "自幂数"]
category: "Java编程"
---
一、实验目的及要求



  1、掌握从键盘输入不同类型数据的方法。



  2、掌握Java语言循环语句的基本格式与编程实现。



  3、认识递归基本原理并用其解决问题。



   



   



   



   



   



   



   



  

 

 

  

  二、实验原理与内容



  1、从键盘输入一个整数，设计一个程序判断该整数是否是完全数。完全数是指其所有因素（包括1但不包括其自身）的和等于该数自身的数。例如28=1+2+4+7+14就是一个完全数。



  2、水仙花数是指个位、十位和百位三个数的立方和等于这个三位数本身的数（提示：水仙花数涉及个位、十位、百位数字，所以水仙花数是一个三位数）。例如153=13+53+33。水仙花数只是自幂数的一种。如果在一个固定的进制中，一个n位自然数等于自身各个数位上数字的n次幂之和，则称此数为自幂数。



  附：十进制中十位以内自然数的自幂数名字：



  一位自幂数：独身数



  两位自幂数：不存在



  三位自幂数：水仙花数



  四位自幂数：四叶玫瑰数，如1634=14+64+34+44



  五位自幂数：五角星数



  六位自幂数：六合数



  七位自幂数：北斗七星数



  八位自幂数：八仙数



  九位自幂数：九九重阳数，如146511208=19+49+69+59+19+19+29+09+89



  十位自幂数：十全十美数



  **请编程实现输出以上所有的自幂数。**



  3、设计一个递归方法int binarySearch(int a

  [],int first, int last, int target)，用于查找给定的数值target是否在已排序的数组a中，其中first是数组a的第一个元素，last是数组a的最后一个元素，方法返回target在a中的位置，如果target不在a中，则返回-（insert_point+1），其中insert_point是要将target插入到a中的位置。示例输出：



  数组a：[1, 3, 4, 8, 13, 20, 26, 88,

  120]



  20在a中的位置：5



  21在a中的位置：-7



  121在a中的位置：-10



  上例中，20在数组a的第5个位置。21不在数组中，如果要将21插入数组a中，-7=-(insert_point+1），插入的位置为6。



  提示：设计程序时要考虑数组a是升序还是降序。



   



   



   



  

 

 

  

  三、实验软硬件环境



  Cmd命令框、java



   



   



   



   



  

 

 

  

  四、实验过程（实验步骤、记录、数据、分析）



  1.代码思路：



   



  代码和注释：



  package

  Test01;



  import java.util.*;



  import

  java.io.*;



  public class

  Test1_1



  {



     

  public static void main(String [] args)throws IOException



     

  {



         

  // 创建Scanner对象用于读取用户输入



         

  Scanner reader=new Scanner(System.in);



         

  int num1, num2;



         

  int result=0; // 定义一个整数变量result用于存储num1的所有因数之和



         

  System.out.print(&quot;请输入一个整数：&quot;);



         

  num1=reader.nextInt(); // 读取用户输入的整数并赋值给num1



         

  // 遍历从1到num1-1的所有整数，检查它们是否是num1的因数



         

  for(int i=1; i&lt;num1; i++){



             

  num2=num1%i; // 计算num1除以i的余数



             

  if(num2==0){ // 如果余数为0，说明i是num1的因数



                 

  result+=i; // 将i累加到result中



             

  }



         

  }



         

  System.out.println(num1+&quot;的所有因数之和为：&quot;+result);



         

  if(result==num1){



             

  System.out.println(num1+&quot;为完全数&quot;);



         

  } else {



             

  System.out.println(num1+&quot;不是完全数&quot;);



         

  }



     

  }



  }



   



  2.代码思路：



   



  代码和注释：



  package

  test01;



  import

  java.util.*;



  import

  java.io.*;



   



  public class

  Test1_2 {



     

  // main方法是程序的入口点，它接受一个字符串数组作为参数



     

  public static void main(String[] args) throws IOException {



         

  int num, num1, num2, num3, num4;



         

  int result = 0;



         

  String str1;



         

  BufferedReader buf;



         

  buf = new BufferedReader(new InputStreamReader(System.in));



         

  System.out.print(&quot;请输入一个正整数：&quot;);



         

  str1 = buf.readLine();



         

  num = Integer.parseInt(str1);



         

  num1 = str1.length();



         

  // 如果输入的数字是两位数，则输出&quot;两位数不存在自幂数&quot;



         

  if (num &gt; 9 &amp;&amp; num &lt; 99) {



             

  System.out.print(&quot;两位数不存在自幂数&quot;);



         

  } else {



             

  // 遍历数字的每一位



             

  for (int i = 0; i &lt; num1; i++) {



             

      num2 = (int) Math.pow(10, num1 - i);



                 

  num3 = ((num % num2) - (num % ((int) Math.pow(10, num1 - i - 1)))) / (int)

  Math.pow(10, num1 - i - 1);



                 

  System.out.println(num + &quot;的第&quot; + (i + 1) + &quot;位为：&quot; + num3);



                 

  num4 = (int) Math.pow(num3, num1);



                 

  result += num4;



             

  }



             

  System.out.println(num + &quot;的各位数字的&quot; + num1 + &quot;次方之和为：&quot; + result);



             

  // 检查这个和是否等于原始数字



             

  if (result == num) {



                 

  System.out.println(num + &quot;为自幂数&quot;);



                 

  // 根据数字位数输出对应的特殊名称



                 

  switch (num1) {



                     

  case 1:



                         

  System.out.println(num + &quot;为独身数&quot;);



                         

  break;



                     

  case 3:



                         

  System.out.println(num + &quot;为水仙花数&quot;);



                         

  break;



                     

  case 4:



                         

  System.out.println(num + &quot;为四叶玫瑰花数&quot;);



                         

  break;



                     

  case 5:



                         

  System.out.println(num + &quot;为五角星数&quot;);



                         

  break;



                     

  case 6:



                         

  System.out.println(num + &quot;为六合数&quot;);



                         

  break;



                     

  case 7:



                         

  System.out.println(num + &quot;为北斗七星数&quot;);



                         

  break;



                     

  case 8:



                         

  System.out.println(num + &quot;为八仙数&quot;);



                         

  break;



                     

  case 9:



                         

  System.out.println(num + &quot;为九九重阳数&quot;);



                         

  break;



                     

  case 10:



                         

  System.out.println(num + &quot;为十全十美数&quot;);



                         

  break;



                     

  default:



                         

  System.out.print(&quot;输入超过十位的正整数！&quot;);



             

      }



             

  } else {



                 

  System.out.println(num + &quot;不是自幂数&quot;);



             

  }



         

  }



     

  }



  }



   



  3.代码思路：



   



  代码和注释：



  package

  test01;



  import

  java.util.*;



  import

  java.io.*;



  import

  java.util.Arrays;



   



  public class

  Test1_3 {



     

  public static void main(String[] args) throws IOException {



         

  int[] arr;



         

  int num1, m, n;



         

  int first, last, target, length;



         

  Scanner reader = new Scanner(System.in); // 创建一个Scanner对象用于读取用户输入



         

  System.out.print(&quot;请输入数组的长度为：&quot;);



         

  length = reader.nextInt(); // 读取用户输入的数组长度并赋值给length



         

  arr = new int[length]; // 根据length创建一个新的整型数组arr



         

  System.out.print(&quot;请输入数组元素：&quot;);



         

  for (int i = 0; i &lt; length; i++) { // 遍历数组，接收用户输入的每个元素



             

  arr[i] = reader.nextInt(); // 将用户输入的元素赋值给数组arr的第i个位置



         

  }



         

  Arrays.sort(arr); // 对数组arr进行排序



         

  System.out.print(&quot;该数组元素为：&quot;);



         

  for (int a : arr) // 使用增强for循环遍历数组arr



             

  System.out.println(a + &quot;    &quot;); // 输出数组元素并在每个元素后添加空格



         

  first = 0; // 初始化first为0，表示数组的第一个元素的索引



      

     last = (length - 1); // 初始化last为length-1，表示数组的最后一个元素的索引



         

  System.out.println(&quot;数组的第一个元素为：&quot; + arr[first]);



         

  System.out.println(&quot;数组的最后一个元素为：&quot; + arr[last]);



         

  System.out.print(&quot;请输入要查找的元素：&quot;);



         

  target = reader.nextInt(); // 读取用户输入的目标值并赋值给target



         

  System.out.println(target + &quot;在x的位置为：&quot;

  + binarySearch(arr, first, last, target)); // 调用binarySearch()方法查找目标值，并输出结果



     

  }



   



     

  // 定义一个递归函数binarySearch()，用于在已排序的数组arr中查找目标值target



     

  public static int binarySearch(int[] x, int first, int last, int target) {



         

  if (first &gt; last) { // 如果first大于last，说明目标值不存在于数组中



             

  return (-first - 1); // 返回一个负数表示未找到目标值



         

  }



         

  int mid = (int) (first + last) / 2; // 计算中间索引mid



         

  int arr1 = x[mid]; // 获取数组arr中索引为mid的元素



         

  if (target &gt; x[mid]) { // 如果目标值大于中间元素，则在右半部分继续查找



             

  return binarySearch(x, mid + 1, last, target);



         

  } else if (target &lt; x[mid]) { // 如果目标值小于中间元素，则在左半部分继续查找



             

  return binarySearch(x, first, mid - 1, target);



         

  } else return mid; // 如果目标值等于中间元素，则返回中间索引mid



     

  }



  }



   



   



  

 

 

  

   



   



   



   



   



   



   



   



   



   



   



   



  

 

 

  

  五、测试/调试及实验结果分析



   



   



   



   



   



   



   



   



   



   



   



   



   



  

 

 

  

  六、实验结论与体会



   



   



   



   



   



   



  年    月    日