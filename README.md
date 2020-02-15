#pragma once
#include "string"
#include "line_data_struct.h"
#include "Boost\Include\boost\property_tree\ptree.hpp"
#include "Boost\Include\boost\property_tree\xml_parser.hpp"
#include "..\mc_line.h"
class LineConfig
{
private:
	LineConfig();
	LineConfig(const LineConfig& that);
	LineConfig& operator=(const LineConfig& that);
public:
	static LineConfig& instance()
	{
		static LineConfig config;
		return config;
	}
	void init(std::string path);
	//获取线路数据
	const std::map<std::string, Line2::LineDataPtr>& get_line()
	{
		return line_data_;
	}
	//获取信号机数据
	std::vector<Line2::SignalPtr>& get_signals()
	{
		return signals_;
	}

	double get_max_lenth()
	{
		return max_lenth_;
	}

	double get_train_lenth()
	{
		return train_lenth_;
	}

	bool& get_turnout_change_state()
	{
		return turnout_is_change_;
	}

	std::map<std::string, mc_line::SignalState>& get_change_signal()
	{
		return signals_change_;
	}

	std::map<std::string, mc_line::TurnoutState>& get_change_switch()
	{
		return switch_change_;
	}

private:
	void read_file();
	void read_line(const boost::property_tree::ptree& tree);
	void read_station(const boost::property_tree::ptree& tree);
	void read_limit_point(const boost::property_tree::ptree& tree);
	void read_signal(const boost::property_tree::ptree& tree);
	Line2::OpenDoor caculate_door(std::string front);
	mc_line::SignalState caculate_signal(std::string state);
	void sort_line_data();
private:
	bool													turnout_is_change_{ false };
	double													max_lenth_{ 1000000.0 };
	double													train_lenth_{ 130 };
	std::string												path_;
	std::vector<Line2::SignalPtr>							signals_;//信号机单独的vector,元素与线路数据里面的信号机指针指向同一内存
	std::map<std::string, mc_line::SignalState>				signals_change_;//状态有变化的信号机
	std::map<std::string, mc_line::TurnoutState>			switch_change_;// 状态有变化的道岔
	std::map<std::string, Line2::LineDataPtr>				line_data_;
};

#define GetLine LineConfig::instance()
