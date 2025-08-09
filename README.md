# dropgacha-and-setdropgacha

# 1 command drop gachanya

static void DGACHA(ENetPeer* peer) {
	// Load JSON file
	std::ifstream file("./database/json/listgacha.json");
	nlohmann::json jsonData;
	file >> jsonData;

	// Get the single quantity value and item IDs
	int quantity = jsonData["quantity"].get<int>();
	std::vector<int> itemIds = jsonData["ids"].get<std::vector<int>>();

	// Pair each item ID with the single quantity
	std::vector<std::pair<int, int>> DgachaList;
	for (const auto& itemId : itemIds) {
		DgachaList.emplace_back(itemId, quantity);
	}

	for (const auto& item : DgachaList) {
		int itemId = item.first;
		int itemQuantity = item.second;

		std::string name_ = pInfo(peer)->world;
		auto it = std::find_if(worlds.begin(), worlds.end(), [name_](const World& a) { return a.name == name_; });
		if (it != worlds.end()) {
			World* world_ = &(*it);
			WorldDrop drop_block_{};
			drop_block_.x = (pInfo(peer)->state == 16 ? pInfo(peer)->x - ((rand() % 12) + 18) : pInfo(peer)->x + ((rand() % 12) + 22));
			drop_block_.y = pInfo(peer)->y + rand() % 16;
			drop_block_.id = itemId;
			drop_block_.count = itemQuantity;

			VisualHandle::Drop(world_, drop_block_);
		}
	}
}




# 2 command set drop gachanya

static void SETDGACHA(ENetPeer* peer, std::string cmd) {
	std::stringstream ss(cmd);
	std::string itemIDsStr, quantityStr;
	ss >> itemIDsStr >> quantityStr;

	if (itemIDsStr.empty() || quantityStr.empty()) {
		VarList::OnConsoleMessage(peer, "Usage: /setdgacha <iditem,iditem,iditem,iditem,iditem> <quantity>");
		return;
	}

	// Parse comma-separated item IDs
	std::vector<int> itemIds;
	std::stringstream idStream(itemIDsStr);
	std::string id;
	while (std::getline(idStream, id, ',')) {
		try {
			itemIds.push_back(std::stoi(id));
		}
		catch (...) {
			VarList::OnConsoleMessage(peer, "Invalid item ID format in: " + id);
			return;
		}
	}

	// Validate quantity
	int quantity = -1;
	try {
		quantity = std::stoi(quantityStr);
	}
	catch (...) {
		VarList::OnConsoleMessage(peer, "Invalid quantity format.");
		return;
	}

	// Load existing JSON file
	std::ifstream inFile("./database/json/listgacha.json");
	nlohmann::json jsonData;
	if (inFile.is_open()) {
		try {
			inFile >> jsonData;
		}
		catch (...) {
			VarList::OnConsoleMessage(peer, "Failed to parse existing config file.");
			inFile.close();
			return;
		}
		inFile.close();
	}
	else {
		VarList::OnConsoleMessage(peer, "Failed to load config file.");
		return;
	}

	// Update JSON data
	jsonData["quantity"] = quantity;
	jsonData["ids"] = itemIds;

	// Save updated JSON to file
	std::ofstream outFile("./database/json/listgacha.json");
	if (outFile.is_open()) {
		outFile << jsonData.dump(4); // Pretty print with 4 spaces
		outFile.close();
		VarList::OnConsoleMessage(peer, ">> Successfully updated prize with " + std::to_string(itemIds.size()) + " item IDs and quantity " + std::to_string(quantity) + ".");
	}
	else {
		VarList::OnConsoleMessage(peer, ">> Failed to save config file.");
	}
}



# 3 isi listgacha.json anda dengan ini

{
    "quantity": 200,
    "ids": [14596, 14084, 5136, 596, 9350]
}


-code by @Rey
- base on Growtavern
- support by my self
