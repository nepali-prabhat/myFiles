# Kalysys v2 notes
## Model to approach the system
Having a clear and dedicated model for approaching the system that we are building is crutial. It helps us make right important decissions moving forward.

The model for the v2 is
1. System requirement analysys
- System Architecture
- Development
- Testing

### System Requirement Analysys
The system requirement analysys for Kalysys v2 is made more easier because of the v1. 

We now have a much more clear vision of what we are building, their relative importance, effectiveness and most importantly their pain points. 

Learning from v1, we will keep all those good decissions that is working and most importantly improve upon them and address the pain points.

`Todo`

### System Architecture
Here we decide on the architectural blueprint of the system.

System architecture can be divided into 2 parts: _A higher level abstraction_ and _low level system design_

The high level abstraction defines what the different entities of our system are and how they interract with each other. This must be decided keeping in mind our development approach such as TDD, CICD, etc. and also the dynamic system we will be building for kalysys.

The low level system design includes selecting the language, database, frameworks and other implementation level details.

__Section notes:__
1. The clean architecture

`// Todo`

### Development
Learning from kalysys v1, we can already decide some key features we want to have in the v2:

1. Test Driven Development `TDD`
- Typed Language: Ts
- CICD: github, s3, invalidation, docker ...
- hooks and redux/recoil

__Section notes:__
1. Testing frameworks: `mocha`, `chai`, `sinon`
- Github rules
- ajv

### Testing 
During the development and after the release we want to test the system that we have built. This is significant for the further growth of the system. Giant, successful companies such as Netflix, Facebook, Linkedin, tiktok would be nowhere are successful without their rigorous testing.

For this we need to have an agile software from the start.

__Section notes:__
1. AB testing
- Feedback and surveys
- Bug report and replication
- crash analytics
- stress testings

## Random notes
As we make a better system in v2, we might come across solutions that can be applied to v1 that can improve it a lot with less effort. We should implement them :)