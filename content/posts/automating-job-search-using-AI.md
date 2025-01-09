+++
title = 'Automating Job Search Using AI'
date = 2025-01-09T02:49:53-06:00
draft = false
+++

As a developer looking for work in 2025, I have to send off potentially hundreds of job applications in order to get enough callbacks and interviews.
I also want to optimize my time spent reading or interacting with job boards to only invest time into the most relevant posts by rating each job and only focusing on the best-rated ones.
Saving time reading through job board posts is an amazing improvement by itself, but this also has the effect of focusing my time on more important job posts.
This experience is ongoing, so be sure to check back for any updates.

It is tedious to read every job description. That sounds silly to complain about, but it is a huge problem when finding remote work.
Consider that when searching for local jobs, you can filter and be specific with what you want, but for remote jobs, you must filter through all of the jobs in the country.
Some jobs are not even remote even though they are labeled as such by the job boards, when it clearly specifies what their work conditions are in the job description.
What I needed to make to solve this was a way to find a bunch of job postings with various keywords from an API, narrow them down, and rate them for relevance to my resume.

# Getting Jobs from API
I chose to use the [Adzuna API](https://developer.adzuna.com/docs/search) to find jobs because it was easy to get access.
This API says it supports paging, but unfortunately, it returns a 500 error when you try to actually use paging in API requests.
That is actually fine, and I have found a workaround by performing a bunch of different searches for different programming languages or processes using the API.
I also sort the results by date, so I usually only get data that has been recently posted to Adzuna, and get up-to-date data every day.

- List of job queries
```bash
$ wc -l jobtypes.txt
      84 jobtypes.txt
$ head jobtypes.txt
remote javascript programmer
remote javascript developer
remote javascript data engineer
remote javascript architect
remote javascript devops
remote javascript aws developer
remote javascript prompt engineer
remote javascript AI
remote node.js programmer
```

I take the API results for each of these job searches and combine together the results, filtering out duplicates.
I can rerun this every day to get a list of newly-posted or newly-discovered jobs from Adzuna.
In addition, sometimes I see a lot of duplicate posts with the same job title and description with different Adzuna job IDs, so I filter these out too.
This seems to happen when a software consulting company posts a duplicate job descriptions for each opening, or when Adzuna scrapes the same job from multiple other job boards, but I haven't proven this for sure.

When asking AI about the relevance of each job, it is best to include the entire description.
I need to rate jobs on multiple criteria, and if only a short description is provided, then usually jobs can filter through that, for example:
- contain the word remote, but are _not_ remote
- do not pertain to my actual experience
- require very specific experience with very specific frameworks
  - (This is ridiculous - there is nothing new under the sun. Get over yourself, jobs, seriously... so what if i don't have experience with React or Next.js or whatever, I can do front-end development, and it's not like these things are hard to learn...)

So anyway, I need to scrape the full job description. I am doing this with a quick python script to get JSON data from the Adzuna landing page for each job.

# Rating the Jobs with AI Prompts
Once I have the full job description, I rate each job by passing it to ChatGPT.
In this prompt, I also include a summary of the candidate (myself) which I created by asking ChatGPT to summarize my resume into a couple short paragraphs.
I need to ask it to rate jobs on multiple criteria.
Here is the role for doing that:

```bash
# prompt-for is a program to print my summary and the job description for the job ID passed in as a parameter. it gets piped into chatgpt as a prompt.

./prompt-for "$id" | chatgpt --model gpt-4o --role 'You are a job hunting assistant.
Your goal is to find remote jobs that will be a good fit.
Given a short description of our job seeker and the job title and details about the position, rate whether or not it is a good fit on a scale 1-10 with 10 being the best possible opportunity.
Return your rating as a number on the first line and a short paragraph of details or clarification about the score on the second line.
Keep this summary brief.

Positive ratings should count toward increasing the score and be comprised of:
- Good fit for programming languages, technologies, and processes
- Experience with technology or processes that they mention or specifically call out
- Remote jobs

Negative ratings should count against the score and be comprised of:
- Job does not seem to be remote or located in the states of MN or WI.
- Preference for experience with a particular front-end framework not mentioned
- Job does not fit with what candidate mentions they are looking for
- Internships
- Jobs requiring a security clearance but not specifying that they will help obtain such a clearance
...
'
```

# Automating Workflow
![screenshot of text user interface workflow](/fzf-job-hunt-workflow.jpg)
I like automating everything.
I even want to automate launching the browser to the specific job and writing a cover letter if the application requires it.
Eventually, I could move some of this stuff into a web app, but the simplest way I have found to rapid-prototype some workflow shortcuts is a bash script that calls `fzf` in a loop and reacts on the selections.
In this case, I list all of the rated jobs, ordered from highest to lowest, and provide the full job description in the `fzf` preview pane.
Once a job is selected, I launch the page in the browser, and prompt for
- removing the job from the remaining list after applying
- ignoring job
- writing a cover letter

## Automatically Writing Cover Letters
If I choose to write a cover letter, it is written using a ChatGPT prompt, saved to a file, and copied to the clipboard.
Then I can easily export the cover letter from Google Drive or paste it directly into a field if the application is designed that way.
```bash
# $filename stores the name of the local file to store the cover letter.

./prompt-for "$id" | chatgpt --model gpt-4o --role 'Your job is to write a short, complete cover letter for a candidate given the candidate description and job description.
This cover letter is for dropping in place in an online job application.
Do not include an address header.
Do not include any placeholders.
Do not include any footer links.
' | tee "$filename"

# copy to mac os clipboard
cat "$filename" | pbcopy
```
