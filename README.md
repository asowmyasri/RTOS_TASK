# ros2_rtos_periodic
#include <rclcpp/rclcpp.hpp>
#include <chrono>
#include <thread>
#include <vector>

using namespace std::chrono_literals;

class TaskScheduler : public rclcpp::Node
{
public:
    TaskScheduler()
        : Node("task_scheduler")
    {
        // Set up periodic timers for each task
        task_a_timer_ = this->create_wall_timer(
            std::chrono::milliseconds(1000), // 1000 ms for A (every 16 ms)
            [this]() { execute_task("A", 4); });

        task_b_timer_ = this->create_wall_timer(
            std::chrono::milliseconds(800), // 800 ms for B (every 12 ms)
            [this]() { execute_task("B", 2); });

        task_c_timer_ = this->create_wall_timer(
            std::chrono::milliseconds(2600), // 2600 ms for C (every 32 ms)
            [this]() { execute_task("C", 12); });
    }

private:
    void execute_task(const std::string &task_id, int exec_time)
    {
        auto start_time = std::chrono::steady_clock::now();
        RCLCPP_INFO(this->get_logger(), "Executing Task %s", task_id.c_str());

        // Simulate task execution
        std::this_thread::sleep_for(std::chrono::milliseconds(exec_time * 100));

        auto end_time = std::chrono::steady_clock::now();
        auto response_time = std::chrono::duration_cast<std::chrono::milliseconds>(end_time - start_time).count();
        
        RCLCPP_INFO(this->get_logger(), "Task %s completed in %ld ms", task_id.c_str(), response_time);
    }

    rclcpp::TimerBase::SharedPtr task_a_timer_;
    rclcpp::TimerBase::SharedPtr task_b_timer_;
    rclcpp::TimerBase::SharedPtr task_c_timer_;
};

int main(int argc, char **argv)
{
    rclcpp::init(argc, argv);
    rclcpp::spin(std::make_shared<TaskScheduler>());
    rclcpp::shutdown();
    return 0;
}

scheduling
