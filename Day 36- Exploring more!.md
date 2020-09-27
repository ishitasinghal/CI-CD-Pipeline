# Day 36- Exploring more!

The `secrets` jobs in the pipeline is an analyzer used by the SAST. 

- This analyzer is for leaked analyzer based on tools like GitLeaks and TruffleHog. 
- This explores the possible secret details that can be leaked in the source code and the files residing in our repository. 
- truffleHog is a Python script that finds risks with secrets in Git repository. 
- GitLeaks is a SAST tool, used to detect hardcoded secrets like tokens and passwords in Git repositories.