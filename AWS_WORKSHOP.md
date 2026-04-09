<!-- Workshop link https://catalog.us-east-1.prod.workshops.aws/join?access-code=1b07-0ad686-15 -->

<!-- You will get this creational -->
export AWS_DEFAULT_REGION="us-west-2"
export AWS_ACCESS_KEY_ID="ASIA..."         # starts with ASIA = temporary/assumed-role key
export AWS_SECRET_ACCESS_KEY="..."
export AWS_SESSION_TOKEN="..."             # session tokens are issued for short-lived access

<!-- call this in the terminal -->
aws sts get-caller-identity

<!-- you will get this output -->

{
    "UserId": "AKIAIOSFODNN7EXAMPLE:Participant",
    "Account": "292510940771",
    "Arn": "arn:aws:sts::292510940771:assumed-role/WSParticipantRole/Participant"
}

<!-- make this directori in the vs code -->

mkdir -p ~/.claude && code ~/.claude/settings.json

<!-- Pase this into your setting.json -->
{
  "model": "opusplan",
  "env": {
    "CLAUDE_CODE_USE_BEDROCK": "1",
    "AWS_REGION": "us-west-2",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "global.anthropic.claude-sonnet-4-6",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "global.anthropic.claude-opus-4-6-v1",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "global.anthropic.claude-haiku-4-5-20251001-v1:0"
  }
}

<!-- now run this in your terminal -->

claude

<!-- you can run cluasde in your folder anyhwewrew in the file system -->


❯ /status                                                                                                                                                                                                                  
          
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   Status   Config   Usage   Stats                                                                                                                                                                                         
                                                                                                                   
  Version: 2.1.97                                                                                                                                                                                                          
  Session name: /rename to add a name                                                    
  Session ID: 438196d9-ed03-46ac-a850-5d5e2bea5aac                                                                                                                                                                         
  cwd: /workshop                                                                                                                                                                                                           
  API provider: Amazon Bedrock
  AWS region: us-west-2

  Model: opusplan (global.anthropic.claude-sonnet-4-6)
  IDE: Connected to VS Code extension version 2.1.97 (server version: 2.1.96)
  Setting sources: User settings
  Esc to cancel



<!-- clode.md for the context stuff on the clude code -->
.clude/clude.md , every prefence you will see

$HOME
└── .claude/
    └── claude.md        # your global preferences

my-project/
├── claude.md            # general project rules
├── backend/
│   ├── claude.md        # backend-specific rules
│   ├── app/
│   │   ├── models.py
│   │   └── handlers.py
│   └── db/
│       └── schema.sql
├── frontend/
│   ├── claude.md        # frontend-specific rules
│   └── src/
│       ├── App.tsx
│       └── components/
│           └── Button.tsx


❯ generate a gernaric CLUDE.md filr for me - this will give me the contest about the project in the future sessions    


% what realy sent to the LLM (to AWS)

%  attach image here
![alt text](image-1.png)

this image show the how LLM work like with the MCP , and all other context..

ask clode API call this is the framework..


% Permission Gates
sometime it genarte the grage bage and it is very confident for that....
but may be that is not important....

% run this
/context

![alt text](image-2.png)


% Build a Q&A App
Deploy an app, where users can anonymously send questions that can be upvoted (no downvoting) with also a profanity filter. 
Have users provide a nickname and make sure they can only upvote the same question once. 
Use api gateway, lambda, and dynamoDB and deploy step-by-step via the AWS CLI for faster deployment. AWS profile is already configured for ap-southeast-2 deployment. 
Create a QR code for the link and then open the image of the QR code. This is just a demo so keep it super minimal.


% this is my app that i have get
![alt text](image-4.png) https://3nwcukw7e8.execute-api.us-east-1.amazonaws.com/



% sub agent also a powerfull stuff 


/cost
![alt text](image-3.png)
this is how you can see the cost
here the stuff split between model, 
anyhting can be fast ==> sent to fast one
anyhting can be thing ==> sent to thing one
if the hello then it will sent to the lower and easy one....


![alt text](image-4.png) https://3nwcukw7e8.execute-api.us-east-1.amazonaws.com/


use sub agents in parallel for different deployment approaches.



Ask about the image
![alt text](image-6.png)


% Ask clude to optmise here is the promp:
Hi claude, i want you to help me design a production version of that qna app i made. ask me any clarifying questions if you're unsure

i got this question
![alt text](image-7.png)

% my answer to the claude code
Multi-event Q&A system with anonymous users (nickname-based), admin moderation, real-time updates via WebSocket, upvote-only system, and AWS-based scalable backend. Supports hundreds to thousands of concurrent users, with archiving after events and optional authentication for admins.






I want to demo how to incorporate a Claude Code workflow with an existing repo. Let's use Locust



![alt text](image-8.png)


/init


I'll explore the Locust codebase to understand its structure, build system, and conventions.



% this is the github link for the  open lib agent

https://github.com/github/spec-kit

We've already got a good start — Locust is cloned at locust and has a CLAUDE.md with project context.

add features

custme metric dashboard



kepp it simple, just test then run 




MCP basically an api


bedrock is not a compute layer




hello

write me a short test python scrpti


What is in the Skill
![alt text](image-9.png)



frontedn design love to use emoji....





this is are not perfect model...

biggest thing is just try this



