2. （扩展练习）请在lab7-answer中分析
  -  cvp->count含义是什么？cvp->count是否可能<0, 是否可能>1？请举例或说明原因。
  > cvp->count的含义是等待这个条件变量的进程个数。
  > cvp->count不可能<0，因为cvp->count是先加再减
  > cvp->count可能>1,因为会有多个进程等待
  
  -  cvp->owner->next_count含义是什么？cvp->owner->next_count是否可能<0, 是否可能>1？请举例或说明原因。
  > cvp->owner->next_count的含义是因发出signal而处于睡眠状态的进程个数。不可能<0，因为是先加后减。不可能>1。假设cvp->owner->next_count = 1,此时进程A发出signal唤醒进程B后处于睡眠状态，而B进程执行结束后又会唤醒A，cvp->owner->next_count会先减1
  
  -  目前的lab7-answer中管程的实现是Hansen管程类型还是Hoare管程类型？请在lab7-answer中实现另外一种类型的管程。
  > Hoare管程类型。

