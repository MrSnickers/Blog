def method
  block_o_code
end

my_new_proc = Proc.new do
object.stuff_done_in_the_proc
end

&my_new_proc <== is now an object!

my_new_lambda = lambda {|thing_passed_in| thing_passed_in.method}
