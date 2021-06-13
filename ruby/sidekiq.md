# sidekiq

Server

client

web monitor

[youtulist](https://www.youtube.com/watch?v=0Q6CbF-ZmB8&list=PLjeHh2LSCFrWGT5uVjUuFKAcrcj5kSai1&index=3)

## web server

[rack](https://github.com/rack/rack)
[puma](https://github.com/puma/puma)

## web framework

[Sinatra](https://github.com/sinatra/sinatra#testing)

## signal handling

TTIN kill ttin thread
USR1 tell the server stop accepting new jobs to work
TERM wait for the server to finish the job and then shut down
sidekiqctl stop

## sidekiq API

mailer = Sidekiq::Queue.new("mailer")

mailer.size

mailer.clear

job = mailer.find_job("job_id")

job.delete

ss = Sidekiq::ScheduledSet.new

rs = Sidekiq::RetrySet.new

ps = Sidekiq::ProcessSet.new

ps.each{|process| p process[:busy]}

ps.each(&:quiet!)

ps.each(&:stop!)

workers = Sidekiq::Workers.new

stats  = Sidekiq::States.new
