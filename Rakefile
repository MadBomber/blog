# frozen_string_literal: true

begin
  require "tocer/rake/register"
rescue LoadError => error
  puts error.message
end

Tocer::Rake::Register.call

task default: :toc
