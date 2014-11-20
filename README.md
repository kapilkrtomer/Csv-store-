Csv-store-
==========



import glob
import phan_proxy
from Queue import Queue
from threading import Thread
import pro_info 
import urllib2
import scrolldriver
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities
import logging
import time
import multiprocessing

logging.basicConfig(level=logging.DEBUG, format='[%(levelname)s] (%(threadName)-10s) %(message)s', )
num_fetch_process = 5

enclosure_queue = Queue()

def store_in_csv(i , q):
   # count =0
    for f1, link in iter(q.get, None):
        try:
            
            pro_info.main(link,f1)
            logging.debug(".......inserted....")
         #   count +=1
        #    logging.debug(count)
        except:
            pass

        time.sleep(1)
        q.task_done()

    q.task_done()



def main3(file1):
   
    f = open(file1)
    filename ="%s.csv"%(file1[:-4])
    f1 =open(filename,"a+")

    proc = []
     
    for i in range(num_fetch_process):
        proc.append(Thread(name = str(i), target= store_in_csv, args=(i, enclosure_queue,)))
        proc[-1].start()
    
    for link in f:
        enclosure_queue.put((f1, link))

    enclosure_queue.join()

    for p in proc:
        enclosure_queue.put(None) 
    
    enclosure_queue.join()

    for p in proc:
        p.join(120)

    print "Done............" 
         
    f.close()
    f1.close()



def main(directory):
    filelist = "%s/*.doc"%(directory)
    filepattern = glob.glob(filelist)
  #  print filepattern
    for file1 in filepattern:
        main3(file1)



if __name__ =="__main__":
    directory = "zivame_dir"
    main(directory)
