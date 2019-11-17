###Αρχιτεκτονική Υπολογιστών Πρώτο Εργαστήριο
###Φατόλας Ελευθέριος ΑΕΜ 8958


1. Σύμφωνα με το αρχείο **_starter_se.py_** που χρησιμοποιήθηκε το σύστημα θα έχει τα εξής χαρακτηριστικά.  
* Αρχικά τα CPU types που μπορούν να περάσουν είναι **minor**, **atomic**, **HPI**.Αν δεν δωθεί στην εντολή ποιον τύπο θέλουμε θα πάρει σαν default τιμή το atomic.
* Ορίζει το **μήκος διεύθυνσης** της κρυφής μνήμης να είναι ίσο με 64 bytes 
* Η **τάση** του συστήματος θα είναι ίση με 3.3 V και ο **ρυθμός του ρολογιού** ίσος με 1 GHz.
* Η **συχνότητα λειτουργίας** της μονάδας επεξεργασίας έχει default τιμή ίση με 4 GHz.
* Το **memory mode** που θα χρησιμοποιηθεί είναι τύπου **timing** και ο **τύπος μνήμης** είναι προκαθορισμένος _DDR3 1600 8x8_.
* Το **μέγεθος της φυσικής μνήμης** έχει default τιμή 2 GB αλλά μπορούμε να το ορίσουμε και εμείς.
* Ο **αριθμός των πυρήνων** είναι 1 εκτός και αν δώσουμε διαφορετική τιμή εμείς.

2. Από τα αρχεία **config.ini** και **config.json** που παράγονται βλέπουμε ότι:  
* ο τύπος της CPU είναι minor (type=MinorCPU)
* ο ρυθμός του ρολογίου είναι στα 1GHz (system.clk_domain   clock=1000)
* το μέγεθος διεύθυνσης της κρυφής μνήμης είναι 64 bytes (cache_line_size=64) 
* το memory mode είναι timing (mem_mode=timing)

Αυτά φαίνονται και στις αντίστοιχες εικόνες στον φάκελο images.

3. O gem5 χρησιμοποιεί τα εξής μοντέλα CPU:  
**BaseSimpleCPU**, ο οποίος χρειάζεται μια κλάση απ την **AtomicSimpleCPU** ή **TimingSimpleCPU** για να τρέξει.Χρησιμοποιείται κυρίως για τον ορισμό συναρτήσεων για τον έλεγχο των διακοπών.
**AtomicSimpleCPU** (η default τιμή που έχει o gem5).Δεν λειτουργούν με διοχέτευση οπότε είναι μια μη ρεαλιστική επιλογή.Ωστόσο είναι πολύ πιο γρήγοροι διότι η πρόσβαση στη μνήμη γίνεται ακαριαία.Για αυτό το λόγο είναι κυρίως χρήσιμοι για την εκκίνηση του υπολογιστή και στη συνέχεια αναλαμβάνει μία ανώτερη μονάδα επεξεργασίας.  
Ένας άλλος **Simple CPU** είναι ο **TimingSimpleCPU**, ο οποίος χρησιμοποιεί timing memory acccesses.Εδώ η πρόσβαση στη μνήμη είναι ρεαλιστική αλλά δεν υπάρχει διοχέτευση.Καθυστερεί την πρόσβαση στην κρυφή μνήμη και περιμένει να πάρει απόφαση απ το σύστημα πριν συνεχίσει.
Ένα άλλο μοντέλο είναι ο **Minor CPU**, o οποίος είναι ένας σειριακός(in-order) επεξεργαστής με διοχέτευση 4 σταδίων.Χρησιμοποιείται για την μοντελοποίηση επεξεργαστών με αυστηρή συμπεριφορά και επιτρέπει την απεικόνιση της θέσης μια εντολής στη γραμμή διοχέτευσης.Τον βρίσκουμε σε περιπτώσεις χαμηλού κόστους ή ισχύος.Δύο μοντέλα που προέρχονται απ τον Minor και ανταποκρίνονται περισσότερο σε υπάρχοντα CPU είναι ο _HPI_ και ο _ex_LITTLE_.

a.  
Η εκτέλεση του προγράμματος test1.c (το οποίο απλά κάνει 2 προσθέσεις και εκτυπώσεις) με τη χρήση του **MinorCPU** είχε τα εξής αποτελέσματα:
> ./build/ARM/gem5.opt -d test1_arm_minorresult configs/example/se.py --cmd=tests/test-progs/hello/bin/alpha/linux/test1_arm --cpu-type=MinorCPU --caches  

final_tick                                   35026000                       # Number of ticks from beginning of simulation (restored from checkpoints and never reset)
host_inst_rate                                  12877                       # Simulator instruction rate (inst/s)
host_mem_usage                                 678788                       # Number of bytes of host memory used
host_op_rate                                    14792                       # Simulator op (including micro ops) rate (op/s)
host_seconds                                     0.69                       # Real time elapsed on the host
host_tick_rate                               50626135                       # Simulator tick rate (ticks/s)
sim_freq                                 1000000000000                       # Frequency of simulated ticks
sim_insts                                        8903                       # Number of instructions simulated
sim_ops                                         10232                       # Number of ops (including micro ops) simulated
sim_seconds                                  0.000035                       # Number of seconds simulated
sim_ticks                                    35026000                       # Number of ticks simulated

* ο πραγματικός χρόνος που παρέμεινε στο σύστημα ήταν 0.69 sec και η προσομοίωση διήρκησε 0.000035 sec.

Ενώ με τη χρήση του **TimingSimpleCPU**:
> ./build/ARM/gem5.opt -d test1_arm_timeresult configs/example/se.py --cmd=tests/test-progs/hello/bin/alpha/linux/test1_arm --cpu-type=TimingSimpleCPU 


final_tick                                  644618000                       # Number of ticks from beginning of simulation (restored from checkpoints and never reset)
host_inst_rate                                  35326                       # Simulator instruction rate (inst/s)
host_mem_usage                                 672388                       # Number of bytes of host memory used
host_op_rate                                    40355                       # Simulator op (including micro ops) rate (op/s)
host_seconds                                     0.25                       # Real time elapsed on the host
host_tick_rate                             2568730327                       # Simulator tick rate (ticks/s)
sim_freq                                 1000000000000                       # Frequency of simulated ticks
sim_insts                                        8847                       # Number of instructions simulated
sim_ops                                         10122                       # Number of ops (including micro ops) simulated
sim_seconds                                  0.000645                       # Number of seconds simulated
sim_ticks                                   644618000                       # Number of ticks simulated

* ο πραγματικός χρόνος που παρέμεινε στο σύστημα ήταν 0.25 sec και η προσομοίωση διήρκησε 0.000645 sec.

b.  
Βλέπουμε οτι με τη χρήση **TimingSimple** μειώθηκε αρκετά ο χρόνος που παρέμεινε στο σύστημα, αλλά ο αριθμός των κύκλων είναι μεγαλύτερος.Παρόλα αυτά βλέπουμε πολλά αποτελέσματα να είναι σχεδόν ίδια, όπως το μέγεθος της μνήμης που χρησιμοποιήθηκε (678784 και 672384 bytes αντίστοιχα), o αριθμός των εντολών (περίπου 8800) και των λειτουργιών (περίπου 10000),διότι ήταν το ίδιο πρόγραμμα.Ακόμα η συχνότητα είναι ίδια στα 1GHz.  

c.  
Mε την αλλαγή της συχνότητας σε 0.5 GHz και της μνήμης σε _DDR4_2400_16x4_ παρατηρούμε τις παρακάτω διαφορές:  
* Στο **Minor** βλέπουμε ότι:

final_tick                                   61188000                       # Number of ticks from beginning of simulation (restored from checkpoints and never reset)
host_inst_rate                                  26155                       # Simulator instruction rate (inst/s)
host_mem_usage                                 678784                       # Number of bytes of host memory used
host_op_rate                                    30027                       # Simulator op (including micro ops) rate (op/s)
host_seconds                                     0.34                       # Real time elapsed on the host
host_tick_rate                              179502807                       # Simulator tick rate (ticks/s)
sim_freq                                 1000000000000                       # Frequency of simulated ticks
sim_insts                                        8903                       # Number of instructions simulated
sim_ops                                         10232                       # Number of ops (including micro ops) simulated
sim_seconds                                  0.000061                       # Number of seconds simulated
sim_ticks                                    61188000                       # Number of ticks simulated

H συχνότητα πήγε απο 2 GHz στα 0.5, αυτό που θέλαμε δηλαδή.Ο χρόνος που παρέμεινε στο σύστημα πήγε απο 0.69 στα 0.34 και η προσομοίωση απο 0.000035 στα 0.000061 sec.Οι εντολές και οι λειτουργίες που εκτελέστηκαν παρέμειναν ίδιες.  

* Στο **TimingSimple** :

final_tick                                  779200000                       # Number of ticks from beginning of simulation (restored from checkpoints and never reset)
host_inst_rate                                  34985                       # Simulator instruction rate (inst/s)
host_mem_usage                                 672132                       # Number of bytes of host memory used
host_op_rate                                    39968                       # Simulator op (including micro ops) rate (op/s)
host_seconds                                     0.25                       # Real time elapsed on the host
host_tick_rate                             3075351240                       # Simulator tick rate (ticks/s)
sim_freq                                 1000000000000                       # Frequency of simulated ticks
sim_insts                                        8847                       # Number of instructions simulated
sim_ops                                         10122                       # Number of ops (including micro ops) simulated
sim_seconds                                  0.000779                       # Number of seconds simulated
sim_ticks                                   779200000                       # Number of ticks simulated

Εδώ βλέπουμε οτι οι διαφορές είναι πολύ μικρότερες.Ο χρόνος που παρέμεινε στο σύστημα δεν άλλαξε και η προσομοίωση πήγε απο 0.000645 στα 0.000779 sec.

Πηγές που χρησιμοποιήθηκαν:   
* [www.gem5/org] (www.gem5/org/docs/html/minor.html) 
* [stackoverflow] (https://stackoverflow.com/questions/tagged/gem5)
* [learning.gem5.org] (http://learning.gem5.org/book/part1/gem5_stats.html) 
* [learning.gem5.org] (http://learning.gem5.org/book/part1/example_configs.html)
* [github] (https://github.com/gem5/gem5)
* [www.gem5/org] (www.gem5.org/CPU_Models)

##ΚΡΙΤΙΚΗ/ΣΧΟΛΙΑ

Η εργασία ήταν σε γενικές γραμμές εύκολη, καθώς όσα ζητήθηκαν ήταν απλά και υπήρχε άφθονο υλικό για βοήθεια τόσο στο διαδίκτυο όσο και στο pdf που δόθηκε.Η πρώτη επαφή με τον gem5 δεν ήταν τόσο "απαιτητική" όσο περίμενα από ένα σύστημα προσομοίωσης αλλά λογικά θα γίνει πιο δύσκολο στα επόμενα εργαστήρια.Επίσης το γεγονός ότι έπρεπε να χρησιμοποιηθεί λειτουργικό linux ήταν ενα μικρό εμπόδιο διότι σε υπολογιστές "παλαιάς" τεχνολογίας η εγκατάσταση του gem5 ήθελε πολύ χρόνο και υπομονή.Θα προτιμούσα να γίνει μεγαλύτερη εμβάθυνση στο πότε και γιατί (με ρεαλιστικά παραδείγματα) επιλέγουμε συγκεκριμένο τύπο επεξεργαστή,μνήμη κτλ.Θεωρώ πως σίγουρα θα μας χρειαστεί τέτοιου είδους γνώση.Ήταν γενικά μια ευχάριστη και χρήσιμη εργασία.


