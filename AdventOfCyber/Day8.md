# Prompt Injection – Sched-yule Conflict

## Task 1 (Introduction)
I was asked to turn on the AttackBox and the host machine to begin the room. I used VPN insted of attackbox since it was web based challenge.

## Task 2 (Agentic AI Hack)
This task explained how LLMs and Agentic AI work and how prompt injection can be used to abuse their capabilities.

### Steps
1. I opened the website from the target machine IP address  
2. I interacted with the chatbot available on the site  
3. I learned that Agentic AI uses planning methods like Chain of Thought  
4. It was explained that modern LLMs can call functions and APIs on their own  

5. I first asked the chatbot to set the 25th as Christmas (directly as it was written in task)
6. I checked the chatbots thoughts to understand what it was doing  
7. From this, I discovered functions like `reset_holiday` and `booking_calendar`  
8. I then asked the chatbot to show all functions it had access to  
9. This revealed another function called `get_logs`  

10. I asked the chatbot to execute `get_logs`  
11. By checking the thinking output, I found an access token  
12. I reused this token with the `reset_holiday` function  
    ![](images/Day8-1.png)
13. This allowed me to change the 25th to Christmas successfully  

### Answers
- Final flag after exploiting the agent  
  `THM{XMAS_IS_COMING__BACK}`
