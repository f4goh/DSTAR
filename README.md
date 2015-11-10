#DSTAR Arduino header Encoding and decoding library#
F4GOH Anthony f4goh@orange.fr <br>

May 2015

Use this library freely with Arduino 1.0.6

## Installation ##
To use the DSTAR library:  
- Go to https://github.com/f4goh/DSTAR, click the [Download ZIP](https://github.com/f4goh/DSTAR/archive/master.zip) button and save the ZIP file to a convenient location on your PC.
- Uncompress the downloaded file.  This will result in a folder containing all the files for the library, that has a name that includes the branch name, usually DSTAR-master.
- Rename the folder to  DSTAR.
- Copy the renamed folder to the Arduino sketchbook\libraries folder.



## Usage notes ##


To use the DSTAR library, see /exemples/Radio_frame_header.ino.

CMX589 DSTAR Modem soon...

73

here is the results :
```c++



```
Have fun with Viterbi <br>
[Download PDF : Viterbi how to](http://users.ece.utexas.edu/~gerstl/ee382v-ics_f09/lectures/Viterbi.pdf) <br>

To Understand pdf, take a look at https://github.com/f4goh/CONVOLUTION <br>


```c++
#include <DSTAR.h>

#define pinLed 13   //Output led

#define BUFSIZE 42          //size of bufferTxRx

//format :  flag1,flag2,flag3,RCV Call,Send Call,Your Call,Own Call 1,Own Call 2
//            1by   1by   1by    8by     8by       8by        8by      4by
byte bufferTxRx[]={0x40,0,0,'F','4','G','O','H',' ',' ','G','F','4','G','O','H',' ',' ','B','C','Q','C','Q','C','Q',' ',' ','F','4','G','O','H',' ',' ',' ','I','D','5','1',0,0,0};

byte bufferConv[BUFSIZE*2];      //buffer convolued,randomize and interleaved data 
byte history[BUFSIZE*8];    //buffer for viterbi decoding (large buffer need)

void convol()
{
  Serial.println("Data to encode :");
  
  print_data(bufferTxRx,sizeof(bufferTxRx),1);     

  Serial.print("Add CRC :");
  Dstar.add_crc(bufferTxRx); 
  
   Serial.print("crc : ");
   Serial.println(Dstar.crc,HEX);    
   Serial.println("Data to encode :");
   
   print_data(bufferTxRx,sizeof(bufferTxRx),0);     
   
   Serial.println("Data convolued :");    

   Dstar.convolution(bufferTxRx,bufferConv);    //source, dest
   print_data(bufferConv,sizeof(bufferConv),0);

   Serial.println("Data interleaving :");    
   Dstar.interleave(bufferConv);  
   
   print_data(bufferConv,sizeof(bufferConv),0); 

   Serial.println("Data scrambling :");    
   Dstar.pseudo_random(bufferConv,660);       
   
   print_data(bufferConv,sizeof(bufferConv),0); 
  
}

void decode()
{

   Serial.println("Data de-scrambling :");    
   Dstar.pseudo_random(bufferConv,660);       
   
   print_data(bufferConv,sizeof(bufferConv),0); 
   
   Serial.println("Data de-interleaving :");    
   Dstar.deInterleave(bufferConv);

   print_data(bufferConv,sizeof(bufferConv),0); 
  
   Serial.println("Data decoded(viterbi) :");    

   Dstar.viterbi(bufferConv,history,bufferTxRx);  //decode data
   print_data(bufferTxRx,sizeof(bufferTxRx),0);     
   
   Serial.print("Nb errors :");
   Serial.println(Dstar.acc_error[1][0]);    //print nb errors

   Serial.print("Checking crc : ");     
   Serial.println(Dstar.check_crc(bufferTxRx));    //crc testing

   Serial.print("Original data :");
   print_data(bufferTxRx,sizeof(bufferTxRx),1);     

}

```

