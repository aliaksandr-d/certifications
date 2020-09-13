Links:
* [Free Code Camp Course](https://www.youtube.com/watch?v=RrKRN9zRBWs)
* [How I Passed the AWS Certified Developer Associate Exam](https://www.freecodecamp.org/news/how-i-passed-the-aws-certified-developer-associate-exam/)
* [AWS Developer Learning Path](https://aws.amazon.com/training/path-developing/)

Distribution:
* Deployment
   * CI/CD
   * Elastic beanstalk
   * Build packages
   * Deploy serverless

* Security
   * Cognito
   * KMS
   * IAM

* Development
   * Lambda
   * Scenarios
   * SDK, CLI, API

* Refactoring
   * Optimize
   * Migration to AWS

* Monitoring and troubleshoot
   * CloudWatch
   * CloudTrail
   * Investigate errors

Whitepapers:
* [AWS Well-Architected Framework](https://d1.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf)
* [Practicing CI and CD on AWS accelerating software delivery for Devops](https://d0.awsstatic.com/whitepapers/DevOps/practicing-continuous-integration-continuous-delivery-on-AWS.pdf)
* [Architecture for the cloud: AWS Best practices](https://d1.awsstatic.com/whitepapers/AWS_Cloud_Best_Practices.pdf)

Elastic Beanstalk:
* Web (ec2 asg elb):
   * Load balanced env (ec2, elb, asg)
   * Single Instance env (ec2, asg (n=1))
* Worker (ec2, asg, sqs) env
* Deployment policies:
   * All at once: 
      * Full redeployment for rollback
      * Cons: downtime
   * Rolling (can't be used for Single-Instance env, requires ELB): 
      * Remove a part of existing servers
      * Create new servers
      * Rollback requires roll update   
   * Rolling with additional batch:
      * Create new few servers
      * Remove old
      * Pros: never reduce capacity
      * Rollback requires roll update 
   * Immutable: 
      * Create new servers+asg inside an Environment (In-Place deploment)
      * Switch ELB to a new ASG 
      * Pros: safest for critical apps
   * Blue/green: 
      * Similar to Immutable but it creates a new EB Environment
      * Switch DNS to a new ELB
      * Cons: slow switch
      * Requires that your DB is out of an Environment

* Configuration files for customization:
   * .ebextensions - hidden folder that contains config files (can define custom image)
   * .config - extension for config files
   * Config files can config:
      * Option settings
      * Linux/Windows server config
      * Custom resources
   * Linux config:
      * Packages in yum
      * Users
      * Groups
      * Files with chmod
      * Commands (execute on ec2)
      * Services
      * Container commands that affects source code
      
* Environment manifest:
   * env.yml
   * Environment name
   * Choose the stack solution
   * ASsociate env links
   * Default config of services
   
* CLI:
   * Install from Github
   * Commands:
      * eb init
      * eb create - create env
      * eb status - env status
      * eb helath - instances health
      * eb events
      * eb logs - view logs
      * eb open - open in browser
      * eb deploy
      * eb config
      * eb terminate
      
* Custom Image: If you need to install a lot of software
   * Take an original ImageId
   * Create a EC2 from image
   * Install custom packages
   * Create a new AMI
   * Update an ENV image
   
* Configure RDS:
   * Inside EB Env:
      * If env is terminated - RDS is terminated
   * Outside EB Env:
      * RDS is remained if EB env is terminated
      
* Notes:
   * Pay only for resources
   * Not recommended for Production
   * Many preconfigured platforms
