# cse-316
project
My case study is based on the multilevel Feedback queue algorithm and it is twisted into two levels i.e.,one working on the basis of Fixed priority preemitive Scheduling and other working on the basis of Round robin scheduling algorithm.For Fixed priority primitive Scheduling algorithm “0” is
the Highest priority and the first queue will processed first then the second queue will be processed on the basis of Round robin algorithm with a time quantum in the multiples of “2”.



CODE:-
#include<bits/stdc++.h>
#include<iostream>
using namespace std;
int max_arrival=-1,min_arrival=INT_MAX;              	 
struct Process
{
    	int pid=0;                                      
    	int priority=0;                                  
    	int arrival_time=0;                              
		int burst_time=0;                                
    	int completion_time=0;                           
    	int turnaround_time=0;                           
		int waiting_time=0;                              
    	int response_time=0;                             
		int remaining_time=0;                            
    	int CPUtime=-1;                                  
};
vector<int> ready_queue;                             	 
bool comparison_Priority(Process a, Process b)       	 
{ 
    	return (a.priority < b.priority); 
}
bool comparison_ArrivalTime(Process a,Process b)     	
{
    	return (a.arrival_time < b.arrival_time);
}
bool comparison_PID(Process a,Process b)             	 
{
    	return (a.pid < b.pid);
}
bool comparison_RemainingTime(Process a,Process b)    	 
{
    	return (a.remaining_time < b.remaining_time);
}
int Enter_Process(int &temp,Process p[],int i)
{
    	cout<<"Process:"<<i+1;
    	temp++;                                      
    	p[i].pid=temp;
    	cout<<"\nEnter Priority:";
    	cin>>p[i].priority;
    	cout<<"Enter Arrival Time:";
    	cin>>p[i].arrival_time;
    	if(p[i].arrival_time<min_arrival)
    	{
				/*Calculating Minimum Arrival time*/
        		min_arrival=p[i].arrival_time;
    	}
    	if(p[i].arrival_time>max_arrival)
    	{
				
        		max_arrival=p[i].arrival_time;          
    	}
    	cout<<"Enter Burst Time:";
    	cin>>p[i].burst_time;
    	p[i].remaining_time=p[i].burst_time;
    	cout<<"\n";
    	return 0;
}
int Show_Process(Process p[],int n,bool b=false)
{
    	if(b==false)
    	{
        		cout<<"\nPID  Priority Arrival Time Burst Time\n";
        		for(int i=0;i<n;i++)
        		{
            			cout<<p[i].pid<<"\t"<<p[i].priority<<"\t\t"<<p[i].arrival_time<<"\t\t"<<p[i].burst_time<<"\n";
        		}
    	}
    	else if(b==true)
    	{
        	
        		cout<<"\nPID  Priority  Arrival Time Burst Time Completion Time  TurnAround Time  Waiting Time  Response Time\n";
        		for(int i=0;i<n;i++)
        		{
            			cout<<p[i].pid<<"\t"<<p[i].priority<<"\t\t"<<p[i].arrival_time<<"\t\t"<<p[i].burst_time<<"\t\t"<<p[i].completion_time<<"\t\t"<<p[i].turnaround_time<<"\t\t"<<p[i].waiting_time<<"\t\t"<<p[i].CPUtime<<"\n";
        		} 
    	}   
    	return 0; 
}
int calculation(Process p[],int n)
{
    	
    	for(int i=0;i<n;i++)
    	{
        		p[i].turnaround_time=p[i].completion_time-p[i].arrival_time;
        		p[i].waiting_time=p[i].turnaround_time-p[i].burst_time;
        		p[i].response_time=p[i].CPUtime-p[i].arrival_time;
    	}
    	return 0;
}
int FPPS(Process p[],int n,int &time)
{
    
    	time=min_arrival;
    	system("CLS");
    	sort(p, p + n, comparison_Priority);
    	sort(p, p + n, comparison_ArrivalTime);
    	int min_priority,k,current,small_priority_index;
    	while(time<=max_arrival)
    	{
        		int small_priority=INT_MAX;
        		for(int i=0;i<n;i++)                           
        		{
            			
            			if(p[i].arrival_time<=time)
            			{
                				current=i;
                				continue;
            			}	
            			else
            			{
                				
                				break;
            			}	
        		}
        		int s=0;
        		while(s<=current)
        		{
        	 			
            			if(p[s].priority<small_priority && p[s].remaining_time!=0)
            			{
                				small_priority=p[s].priority;
                				small_priority_index=s;
            			}
            			s++;
       			}
        		
				p[small_priority_index].remaining_time--;
        		if(p[small_priority_index].CPUtime==-1)
        		{
         				
            			p[small_priority_index].CPUtime=time;
        		}
        		time++;
       			if(p[small_priority_index].remaining_time==0)
        		{
            		
            			p[small_priority_index].completion_time=time;
        		}
    	}
    	
    	int remaining_time=p[small_priority_index].remaining_time;
    	if(p[small_priority_index].remaining_time==0)
    	{
    		
        		return 0;
    	} 
    	p[small_priority_index].remaining_time=0;
    	if(p[small_priority_index].CPUtime==-1)
    	{
        		
        		p[small_priority_index].CPUtime=time;
    	}
    	time+=remaining_time;
    	if(p[small_priority_index].remaining_time==0)
    	{
        	
        		p[small_priority_index].completion_time=time;
    	} 	
    	return 0;
}
int Round_Robin(Process p[],int n,int tq,int &time)    //Round Robin Scheduling
{
    	/*Round Robin Scheduling*/
    	int start=-1,remaining_time=-1,cur=-1;
    	sort(p,p+n,comparison_RemainingTime);              
    	for(int i=0;i<n;i++)
    	{
        		
        		if(p[i].remaining_time==0)
        		{
            			continue;
        		}
        		else
        		{
            			start=i;
            			break;
        		}
    	}
    	sort(p+start,p+n,comparison_ArrivalTime);                
    	for(int i=0;i<n;i++)
    	{
       			if(p[i].remaining_time==0)
        		{
            		
            			if(!ready_queue.empty())
            			{
                				cur=ready_queue[0];
                				ready_queue.erase(ready_queue.begin());
                				if(p[cur].remaining_time<=tq)
                				{
                    					
                    					remaining_time=p[cur].remaining_time;
                    					p[cur].remaining_time=0;
                    					time+=remaining_time;
                    					p[cur].completion_time=time;
                				}	
                				else
                				{
                    				
                    					p[cur].remaining_time-=tq;
                    					time+=tq;
                    					ready_queue.push_back(cur);
                				}	
            			}
            			continue;
        		}
        		else
        		{
        				if(p[i].arrival_time<=time)
        				{
                				if(p[i].remaining_time<=tq)
                				{
                    					
                    					remaining_time=p[i].remaining_time;
                    					p[i].remaining_time=0;
                    					if(p[i].CPUtime==-1)
                    					{
                        					
                        						p[i].CPUtime=time;
                    					}
                    					time+=remaining_time;
                				}
                				else
                				{
                    				
                    					p[i].remaining_time-=tq;
                    					if(p[i].CPUtime==-1)
                    					{
                        					
                        						p[i].CPUtime=time;
                    					}	
                    					time+=tq;
                    					ready_queue.push_back(i);
                				}
            			}
            			else
            			{
                				if(!ready_queue.empty())
                				{
                    					cur=ready_queue[0];
                   						ready_queue.erase(ready_queue.begin());
                    					if(p[cur].remaining_time<=tq)
                    					{
                        					
                        						remaining_time=p[cur].remaining_time;
                        						p[cur].remaining_time=0;
                        						time+=remaining_time;
                        						p[cur].completion_time=time;
                    					}
                    					else
                    					{
                        						
                        						p[cur].remaining_time-=tq;
                        						time+=tq;
                        						ready_queue.push_back(cur);
                    					}
                				}
       	     			}
       			}
    	}
		while(!ready_queue.empty())
    	{
        		/*
        		Executes all the processes in ready queue
        		*/
        		cur=ready_queue[0];
        		ready_queue.erase(ready_queue.begin());
        		if(p[cur].remaining_time<=tq)
        		{
            		
            			remaining_time=p[cur].remaining_time;
            			p[cur].remaining_time=0;
            			time+=remaining_time;
            			p[cur].completion_time=time;
        		}
        		else
        		{
            			
            			p[cur].remaining_time-=tq;
            			time+=tq;
            			ready_queue.push_back(cur);
        		}
    	}
    	return 0;
}
int main()
{                    
    	int n,temp=0,time_q,time=0;
    	cout<<"\t\t\tMUlti level queue Scheduling\n\t\t\t\t\t\t-\n";
    	cout<<"Enter No. Of Processes:";
    	cin	>>n;
    	Process p[n];
    	cout<<"\n";
    	for(int i=0;i<n;i++)
    	{
        		Enter_Process(temp,p,i);
   	 	}
   	 	cout<<"Successfully Added The Process:";
   	 	Show_Process(p,n);
    	cout<<"Enter Time Quantum(Multiples Of Two):";
    	cin>>time_q;
    	while(time_q%2!=0)
    	{
        		
        		cout<<"\t\t*ERROR*\n";
        		cout<<"Enter Time In Multiples Of 2:";
        		cin>>time_q;
    	}
    	FPPS(p,n,time);                        //Fixed Priority Preemtive Scheduling
    	Round_Robin(p,n,time_q,time);          //Round Robin Scheduling
    	calculation(p,n);
    	sort(p,p+n,comparison_PID);
    	Show_Process(p,n,true); 
    	cout<<"\n";
		cout<<"All Process Completed In "<<time<<" unit time.\n\n";
    	system("pause")
return 0;
}

