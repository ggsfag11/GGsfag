local P=game:GetService("Players").LocalPlayer
local U=game:GetService("UserInputService")
local R=game:GetService("RunService")
local T=game:GetService("TweenService")
local G=Instance.new("ScreenGui",P:WaitForChild("PlayerGui"))
G.Name="RGBStorm";G.ResetOnSpawn=false
local F=Instance.new("Frame",G)
F.Size=UDim2.fromOffset(320,220);F.Position=UDim2.new(.5,-160,.5,-110);F.BackgroundColor3=Color3.fromRGB(15,15,30);F.Active=true
Instance.new("UICorner",F).CornerRadius=UDim.new(0,16)
local S=Instance.new("UIStroke",F);S.Thickness=2
local GR=Instance.new("UIGradient",S)
GR.Color=ColorSequence.new({ColorSequenceKeypoint.new(0,Color3.fromRGB(255,0,100)),ColorSequenceKeypoint.new(.5,Color3.fromRGB(0,200,255)),ColorSequenceKeypoint.new(1,Color3.fromRGB(180,0,255))})
local function btn(text,pos,size)
 local b=Instance.new("TextButton",F);b.Position=pos;b.Size=size;b.Text=text;b.TextColor3=Color3.new(1,1,1);b.BackgroundColor3=Color3.fromRGB(40,40,75);b.Font=Enum.Font.GothamBold;b.TextSize=16;Instance.new("UICorner",b).CornerRadius=UDim.new(0,10);return b
end
local H=btn("⚡ RGB STORM",UDim2.fromOffset(8,5),UDim2.new(1,-52,0,38))
local C=btn("−",UDim2.new(1,-42,0,5),UDim2.fromOffset(34,34))
local B=btn("✈ FLY: OFF",UDim2.fromOffset(15,55),UDim2.new(1,-30,0,45))
local X=Instance.new("TextBox",F);X.Position=UDim2.fromOffset(15,110);X.Size=UDim2.new(1,-30,0,36);X.Text="100";X.PlaceholderText="Speed 50-300";X.ClearTextOnFocus=false;X.TextColor3=Color3.new(1,1,1);X.BackgroundColor3=Color3.fromRGB(30,30,50);X.Font=Enum.Font.Gotham;X.TextSize=16;Instance.new("UICorner",X).CornerRadius=UDim.new(0,8)
local UP=btn("▲ UP",UDim2.fromOffset(15,158),UDim2.new(.5,-20,0,38))
local DN=btn("▼ DOWN",UDim2.new(.5,5,0,158),UDim2.new(.5,-20,0,38))
local flying=false;local speed=100;local keys={};local vertical=0;local att,lv,ao;local saved={};local oldAuto;local collapsed=false
local function stop()
 flying=false
 if lv then lv:Destroy();lv=nil end;if ao then ao:Destroy();ao=nil end;if att then att:Destroy();att=nil end
 local ch=P.Character;local h=ch and ch:FindFirstChildOfClass("Humanoid")
 if h then h.AutoRotate=oldAuto~=nil and oldAuto or true end
 for part,value in pairs(saved)do if part.Parent then part.CanCollide=value end end;table.clear(saved);B.Text="✈ FLY: OFF"
end
local function toggle()
 if flying then stop();return end
 local ch=P.Character;local h=ch and ch:FindFirstChildOfClass("Humanoid");local root=ch and ch:FindFirstChild("HumanoidRootPart")
 if not h or not root or h.Health<=0 then return end
 flying=true;oldAuto=h.AutoRotate;h.AutoRotate=false
 for _,v in ipairs(ch:GetDescendants())do if v:IsA("BasePart")then saved[v]=v.CanCollide;v.CanCollide=false end end
 att=Instance.new("Attachment",root)
 lv=Instance.new("LinearVelocity",root);lv.Attachment0=att;lv.RelativeTo=Enum.ActuatorRelativeTo.World;lv.VelocityConstraintMode=Enum.VelocityConstraintMode.Vector;lv.MaxForce=math.huge
 ao=Instance.new("AlignOrientation",root);ao.Attachment0=att;ao.Mode=Enum.OrientationAlignmentMode.OneAttachment;ao.MaxTorque=math.huge;ao.Responsiveness=15
 B.Text="✈ FLY: ON"
end
B.Activated:Connect(toggle)
X.FocusLost:Connect(function()speed=math.clamp(tonumber(X.Text)or 100,50,300);X.Text=tostring(speed)end)
UP.InputBegan:Connect(function()vertical=1 end);UP.InputEnded:Connect(function()vertical=0 end)
DN.InputBegan:Connect(function()vertical=-1 end);DN.InputEnded:Connect(function()vertical=0 end)
U.InputBegan:Connect(function(i,g)if g then return end;keys[i.KeyCode]=true;if i.KeyCode==Enum.KeyCode.E then toggle()end end)
U.InputEnded:Connect(function(i)keys[i.KeyCode]=false end)
R.RenderStepped:Connect(function(dt)
 GR.Rotation=(GR.Rotation+dt*100)%360
 if not flying or not lv or not ao then return end
 local cam=workspace.CurrentCamera;if not cam then return end
 local d=Vector3.zero
 if keys[Enum.KeyCode.W]or keys[Enum.KeyCode.Up]then d+=cam.CFrame.LookVector end
 if keys[Enum.KeyCode.S]or keys[Enum.KeyCode.Down]then d-=cam.CFrame.LookVector end
 if keys[Enum.KeyCode.A]or keys[Enum.KeyCode.Left]then d-=cam.CFrame.RightVector end
 if keys[Enum.KeyCode.D]or keys[Enum.KeyCode.Right]then d+=cam.CFrame.RightVector end
 local y=vertical
 if keys[Enum.KeyCode.Space]then y=1 elseif keys[Enum.KeyCode.LeftControl]then y=-1 end
 d+=Vector3.yAxis*y
 lv.VectorVelocity=d.Magnitude>0 and d.Unit*speed or Vector3.zero
 local look=cam.CFrame.LookVector;local flat=Vector3.new(look.X,0,look.Z)
 if flat.Magnitude>.01 then ao.CFrame=CFrame.lookAt(Vector3.zero,flat)end
end)
local dragging=false;local origin,start
H.InputBegan:Connect(function(i)if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then dragging=true;origin=i.Position;start=F.Position end end)
U.InputChanged:Connect(function(i)if dragging and origin and start and(i.UserInputType==Enum.UserInputType.MouseMovement or i.UserInputType==Enum.UserInputType.Touch)then local d=i.Position-origin;F.Position=UDim2.new(start.X.Scale,start.X.Offset+d.X,start.Y.Scale,start.Y.Offset+d.Y)end end)
U.InputEnded:Connect(function(i)if i.UserInputType==Enum.UserInputType.MouseButton1 or i.UserInputType==Enum.UserInputType.Touch then dragging=false end end)
C.Activated:Connect(function()collapsed=not collapsed;B.Visible=not collapsed;X.Visible=not collapsed;UP.Visible=not collapsed;DN.Visible=not collapsed;T:Create(F,TweenInfo.new(.2),{Size=collapsed and UDim2.fromOffset(320,45)or UDim2.fromOffset(320,220)}):Play();C.Text=collapsed and "+"or "−"end)
P.CharacterAdded:Connect(function()if flying then stop()end end)
