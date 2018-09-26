这里的run不是指线程体的重写run方法。

> start（）:通过该方法启动线程的同时也创建了一个线程，真正实现了多线程,这是无需等待run（）方法中的代码执行完毕就可以直接执行线程外面的代码，通过start创建的线程处于可运行状态，当得到CPU时间片后就会执行其中的run方法， 这里方法run（）称为线程体，它包含了要执行的这个线程的内容，Run方法运行结束， 此线程随即终止。

> run（）：在当前线程开启,比如当前线程是主线程,那么运行在主线程,如果当前线程是子线程，那么在子线程运行。还是要顺序执行，还是要等待run方法体执行完毕后才可继续执行下面的代码，这样就没有达到写线程的目的。

## 示范一:在主线程通过start方法开启线程

```
  new Thread(new Runnable() {
                @Override
                public void run() {//Thread-5878
                    System.out.println("====本线程是通过start方式开启的,会单独开启一个线程==1==="+currentThread().getName());
                }
            }).start();
```

## 示范二:在主线程通过run方法开启线程

```
  new Thread(new Runnable() {//Thread-main
                @Override
                public void run() {
                    System.out.println("====本线程是通过在主线程中run方式开启的,并没有创建一个线程==2==="+currentThread().getName());
                }
            }).run();//run开启是在当前线程
```

## 示范三:在子线程通过run方法开启新线程

```
new Thread(new Runnable() {
                @Override
                public void run() {
                    new Thread(new Runnable() {
                        @Override
                        public void run() {//run是在当前线程运行也就是外层的子线程   Thread-5880
                            System.out.println("====本线程是通过在子线程中通过run方式开启的,并没有创建一个线程==3==="+currentThread().getName());
                        }
                    }).run();//run开启是在当前线程
                }
            }).start();
```

 

 

 

 

 