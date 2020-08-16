# Quick-Importer
Current Source Code for the Quick Importer Plugin
-- // SERVICES \\ --
local Players = game:GetService("Players")
local Groups = game:GetService("GroupService")
local InsertService = game:GetService("InsertService")
local MarketPlace = game:GetService("MarketplaceService")

-- // WIDGET INFO \\ --
local widgetInfo = DockWidgetPluginGuiInfo.new(
	Enum.InitialDockState.Float,
	false,
	false,
	300,
	350,
	250,
	300
)

-- // WIDGET \\ --
local widget = plugin:CreateDockWidgetPluginGui("Quick Importer", widgetInfo)
widget.Title = "Quick Importer"

-- // TOOLBAR \\ --
local toolbar = plugin:CreateToolbar("Quick Importer")
local toggle = toolbar:CreateButton("Toggle Importer", "Toggle the asset importer", "rbxassetid://12943823")

toggle.Click:Connect(function()
	widget.Enabled = not widget.Enabled
	widget.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
end)

-- // WIDGET ITEMS \\ --

-- Type of asset (model or bundle)
local assetType = Instance.new("TextLabel")
assetType.Text = "Asset Type: Model"
assetType.TextScaled = true
assetType.Size = UDim2.new(1, 0, 0.2, 0)
assetType.Position = UDim2.new(0, 0, 0, 0)
assetType.Visible = true
assetType.BackgroundColor3 = Color3.fromRGB(50, 100, 150)
assetType.TextColor3 = Color3.fromRGB(0, 0, 0)
assetType.Font = Enum.Font.SourceSans
assetType.BorderSizePixel = 0
assetType.Parent = widget

-- The assetId of the item to import (it's an integer/number)
local assetToImport = Instance.new("TextBox")
assetToImport.Text = "Asset to Import: "
assetToImport.TextScaled = true
assetToImport.Size = UDim2.new(1, 0, 0.2, 0)
assetToImport.Position = UDim2.new(0, 0, 0.2, 0)
assetToImport.Visible = true
assetToImport.BackgroundColor3 = Color3.fromRGB(82, 124, 174)
assetToImport.TextColor3 = Color3.fromRGB(0, 0, 0)
assetToImport.Font = Enum.Font.SourceSans
assetToImport.BorderSizePixel = 0
assetToImport.Parent = widget

-- Confirm button to load in the asset
local confrimImport = Instance.new("TextButton")
confrimImport.Text = "Confirm Import"
confrimImport.Size = UDim2.new(1, 0, 0.25, 0)
confrimImport.Position = UDim2.new(0, 0, 0.75, 0)
confrimImport.Visible = true
confrimImport.TextScaled = true
confrimImport.BackgroundColor3 = Color3.fromRGB(33, 84, 185)
confrimImport.TextColor3 = Color3.fromRGB(0, 0, 0)
confrimImport.Font = Enum.Font.SourceSans
confrimImport.BorderSizePixel = 0
confrimImport.Parent = widget

-- Creator info stuff
local creatorInfo = Instance.new("TextLabel")
creatorInfo.Text = "Creator Info: "
creatorInfo.Size = UDim2.new(0.75, 0, 0.35, 0)
creatorInfo.TextScaled = true
creatorInfo.Position = UDim2.new(0, 0, 0.4, 0)
creatorInfo.Visible = true
creatorInfo.BackgroundColor3 = Color3.fromRGB(50, 100, 150)
creatorInfo.TextColor3 = Color3.fromRGB(0, 0, 0)
creatorInfo.Font = Enum.Font.SourceSans
creatorInfo.BorderSizePixel = 0
creatorInfo.Parent = widget
local creatorImage = Instance.new("ImageLabel")
creatorImage.Size = UDim2.new(0.25, 0, 0.35, 0)
creatorImage.Position = UDim2.new(0.75, 0, 0.4, 0)
creatorImage.BorderSizePixel = 0
creatorImage.Visible = true

-- // WIDGET FUNCTIONS \\ --
local function importAsset()
	local asset = assetToImport.Text
	if tonumber(asset) then
		asset = tonumber(asset)
		local a
		local success, err = pcall(function()
			a = InsertService:LoadAsset(asset)
		end)
		if success then
			a.Parent = workspace
			a.Name = "Asset Model: ".. tostring(asset)
		else
			warn(err)
		end
	else
		warn(asset.." isn't a valid number.")
	end
end

local function getOwner()
	local asset = assetToImport.Text
	if tonumber(asset) then
		local info = MarketPlace:GetProductInfo(asset, Enum.InfoType.Asset)
		if info.Creator.CreatorType == "User" then
			creatorInfo.Text = "Creator Info: Name - ".. info.Creator.Name.." | Id: ".. tostring(info.Creator.CreatorTargetId).." | Asset Name: ".. info.Name
			local content, isReady = Players:GetUserThumbnailAsync(info.Creator.CreatorTargetId, Enum.ThumbnailType.HeadShot, Enum.ThumbnailSize.Size150x150)
			creatorImage.Image = content
			creatorImage.Parent = widget
		else
			creatorInfo.Text = "Group Info: Name - ".. info.Creator.Name.." | Id: ".. tostring(info.Creator.CreatorTargetId).." | Asset Name: ".. info.Name
			local groupInfo
			local success, err = pcall(function()
				groupInfo = Groups:GetGroupInfoAsync(info.Creator.CreatorTargetId)
			end)
			if not success then warn(err) else
				creatorImage.Image = groupInfo.EmblemUrl
			end
		end
	else
		warn(asset.." isn't a valid number.")
	end
end

confrimImport.MouseButton1Click:Connect(importAsset)
assetToImport:GetPropertyChangedSignal("Text"):Connect(getOwner)
