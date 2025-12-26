import { useState } from "react";
import { Button } from "./ui/button";
import { Input } from "./ui/input";
import { Label } from "./ui/label";
import { Checkbox } from "./ui/checkbox";
import { Card, CardContent, CardDescription, CardHeader, CardTitle } from "./ui/card";
import { MapPin, Eye, EyeOff, MapPinned } from "lucide-react";

export function LoginForm() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [rememberMe, setRememberMe] = useState(false);
  const [showPassword, setShowPassword] = useState(false);

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    // Handle login logic here
    console.log("Login attempt:", { email, password, rememberMe });
  };

  return (
    <Card className="w-full max-w-md mx-4 shadow-xl">
      <CardHeader className="space-y-3 text-center">
        <div className="flex items-center justify-center gap-3 mb-2">
          <div className="p-3 bg-gradient-to-br from-green-600 to-green-700 rounded-xl shadow-lg">
            <MapPinned className="size-10 text-white" strokeWidth={2.5} />
          </div>
        </div>
        <CardTitle className="text-3xl bg-gradient-to-r from-green-600 to-green-700 bg-clip-text text-transparent">GeoAlert</CardTitle>
        <CardDescription>
          Sign in to your account to continue
        </CardDescription>
      </CardHeader>
      <CardContent>
        <form onSubmit={handleSubmit} className="space-y-4">
          <div className="space-y-2">
            <Label htmlFor="email">Email</Label>
            <Input
              id="email"
              type="email"
              placeholder="you@example.com"
              value={email}
              onChange={(e) => setEmail(e.target.value)}
              required
            />
          </div>

          <div className="space-y-2">
            <Label htmlFor="password">Password</Label>
            <div className="relative">
              <Input
                id="password"
                type={showPassword ? "text" : "password"}
                placeholder="Enter your password"
                value={password}
                onChange={(e) => setPassword(e.target.value)}
                required
                className="pr-10"
              />
              <button
                type="button"
                onClick={() => setShowPassword(!showPassword)}
                className="absolute right-3 top-1/2 -translate-y-1/2 text-gray-500 hover:text-gray-700"
              >
                {showPassword ? (
                  <EyeOff className="size-4" />
                ) : (
                  <Eye className="size-4" />
                )}
              </button>
            </div>
          </div>

          <div className="flex items-center justify-between">
            <div className="flex items-center space-x-2">
              <Checkbox
                id="remember"
                checked={rememberMe}
                onCheckedChange={(checked) => setRememberMe(checked as boolean)}
              />
              <Label
                htmlFor="remember"
                className="cursor-pointer"
              >
                Remember me
              </Label>
            </div>
            <a
              href="#"
              className="text-sm text-green-600 hover:text-green-800 hover:underline"
            >
              Forgot password?
            </a>
          </div>

          <Button type="submit" className="w-full bg-green-600 hover:bg-green-700">
            Sign In
          </Button>

          <div className="text-center text-sm text-gray-600">
            Don't have an account?{" "}
            <a href="#" className="text-green-600 hover:text-green-800 hover:underline">
              Sign up
            </a>
          </div>
        </form>
      </CardContent>
    </Card>
  );
} //signin page code
import { useState } from "react";
import { LoginForm } from "./components/LoginForm";
import { WelcomePage } from "./components/WelcomePage";

export default function App() {
  const [showWelcome, setShowWelcome] = useState(true);

  if (showWelcome) {
    return <WelcomePage onNext={() => setShowWelcome(false)} />;
  }

  return (
    <div className="size-full flex items-center justify-center bg-gradient-to-br from-green-50 to-emerald-100">
      <LoginForm />
    </div>
  );
}
import { useState } from "react";
import { useForm } from "react-hook-form";
import { 
  CloudRain, 
  Mountain, 
  Droplets, 
  Trees, 
  Activity, 
  Waves,
  CircleAlert,
  CircleCheck
} from "lucide-react";

type SoilType = "gravel" | "sand" | "silt";

interface LandslideFormData {
  rainfall: number;
  slopeAngle: number;
  soilSaturation: number;
  vegetationCover: number;
  earthquakeActivity: 0 | 1;
  proximityToWater: number;
  soilType: SoilType;
}

interface PredictionResult {
  risk: boolean;
  riskScore: number;
}

export default function App() {
  const [prediction, setPrediction] = useState<PredictionResult | null>(null);
  
  const {
    register,
    handleSubmit,
    formState: { errors },
    watch
  } = useForm<LandslideFormData>({
    defaultValues: {
      earthquakeActivity: 0,
      soilType: "gravel"
    }
  });

  const earthquakeValue = watch("earthquakeActivity");

  const onSubmit = (data: LandslideFormData) => {
    // Mock prediction algorithm based on risk factors
    let riskScore = 0;
    
    // Rainfall contribution (higher = more risk)
    riskScore += (data.rainfall / 300) * 25;
    
    // Slope angle contribution (higher = more risk)
    riskScore += (data.slopeAngle / 90) * 20;
    
    // Soil saturation contribution (higher = more risk)
    riskScore += data.soilSaturation * 20;
    
    // Vegetation cover (lower = more risk)
    riskScore += (1 - data.vegetationCover) * 15;
    
    // Earthquake activity (major risk factor)
    riskScore += data.earthquakeActivity * 10;
    
    // Proximity to water (closer = more risk, assuming in meters)
    riskScore += Math.max(0, (100 - data.proximityToWater) / 100) * 10;
    
    // Soil type contribution
    const soilRisk = {
      gravel: 0,
      sand: 5,
      silt: 10
    };
    riskScore += soilRisk[data.soilType];
    
    const risk = riskScore >= 50;
    
    setPrediction({ risk, riskScore: Math.min(100, riskScore) });
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-emerald-50 via-teal-50 to-green-50 py-8 px-4">
      <div className="max-w-3xl mx-auto">
        {/* Header */}
        <div className="text-center mb-8">
          <div className="flex items-center justify-center gap-3 mb-3">
            <Mountain className="w-10 h-10 text-emerald-700" />
            <h1 className="text-emerald-900">Landslide Prediction System</h1>
          </div>
          <p className="text-muted-foreground">
            Enter environmental and geophysical data to assess landslide risk
          </p>
        </div>

        {/* Main Form Card */}
        <div className="bg-white rounded-2xl shadow-xl border border-emerald-100 overflow-hidden">
          <form onSubmit={handleSubmit(onSubmit)} className="p-8">
            
            {/* Environmental Parameters Section */}
            <div className="mb-8">
              <div className="flex items-center gap-2 mb-5 pb-2 border-b-2 border-emerald-200">
                <CloudRain className="w-5 h-5 text-emerald-600" />
                <h2 className="text-emerald-900">Environmental Parameters</h2>
              </div>
              
              <div className="space-y-5">
                {/* Rainfall */}
                <div>
                  <label htmlFor="rainfall" className="block text-gray-700 mb-2">
                    Rainfall (mm)
                  </label>
                  <input
                    id="rainfall"
                    type="number"
                    step="0.1"
                    placeholder="e.g., 0 - 300"
                    className="w-full px-4 py-3 bg-gray-50 border border-gray-300 rounded-lg focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition-all"
                    {...register("rainfall", {
                      required: "Rainfall is required",
                      min: { value: 0, message: "Rainfall cannot be negative" },
                      valueAsNumber: true
                    })}
                  />
                  {errors.rainfall && (
                    <p className="text-red-600 mt-1">{errors.rainfall.message}</p>
                  )}
                </div>

                {/* Vegetation Cover */}
                <div>
                  <label htmlFor="vegetationCover" className="block text-gray-700 mb-2">
                    <span className="flex items-center gap-2">
                      <Trees className="w-4 h-4 text-green-600" />
                      Vegetation Cover (0 - 1)
                    </span>
                  </label>
                  <input
                    id="vegetationCover"
                    type="number"
                    step="0.01"
                    placeholder="e.g., 0.0 (bare) to 1.0 (full coverage)"
                    className="w-full px-4 py-3 bg-gray-50 border border-gray-300 rounded-lg focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition-all"
                    {...register("vegetationCover", {
                      required: "Vegetation cover is required",
                      min: { value: 0, message: "Must be between 0 and 1" },
                      max: { value: 1, message: "Must be between 0 and 1" },
                      valueAsNumber: true
                    })}
                  />
                  {errors.vegetationCover && (
                    <p className="text-red-600 mt-1">{errors.vegetationCover.message}</p>
                  )}
                </div>

                {/* Proximity to Water */}
                <div>
                  <label htmlFor="proximityToWater" className="block text-gray-700 mb-2">
                    <span className="flex items-center gap-2">
                      <Waves className="w-4 h-4 text-blue-600" />
                      Proximity to Water (meters)
                    </span>
                  </label>
                  <input
                    id="proximityToWater"
                    type="number"
                    step="0.1"
                    placeholder="e.g., distance in meters"
                    className="w-full px-4 py-3 bg-gray-50 border border-gray-300 rounded-lg focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition-all"
                    {...register("proximityToWater", {
                      required: "Proximity to water is required",
                      min: { value: 0, message: "Distance cannot be negative" },
                      valueAsNumber: true
                    })}
                  />
                  {errors.proximityToWater && (
                    <p className="text-red-600 mt-1">{errors.proximityToWater.message}</p>
                  )}
                </div>
              </div>
            </div>

            {/* Geophysical Parameters Section */}
            <div className="mb-8">
              <div className="flex items-center gap-2 mb-5 pb-2 border-b-2 border-amber-200">
                <Mountain className="w-5 h-5 text-amber-600" />
                <h2 className="text-amber-900">Geophysical Parameters</h2>
              </div>
              
              <div className="space-y-5">
                {/* Slope Angle */}
                <div>
                  <label htmlFor="slopeAngle" className="block text-gray-700 mb-2">
                    Slope Angle (degrees)
                  </label>
                  <input
                    id="slopeAngle"
                    type="number"
                    step="0.1"
                    placeholder="e.g., 0 - 90"
                    className="w-full px-4 py-3 bg-gray-50 border border-gray-300 rounded-lg focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition-all"
                    {...register("slopeAngle", {
                      required: "Slope angle is required",
                      min: { value: 0, message: "Angle must be between 0 and 90" },
                      max: { value: 90, message: "Angle must be between 0 and 90" },
                      valueAsNumber: true
                    })}
                  />
                  {errors.slopeAngle && (
                    <p className="text-red-600 mt-1">{errors.slopeAngle.message}</p>
                  )}
                </div>

                {/* Earthquake Activity */}
                <div>
                  <label className="block text-gray-700 mb-2">
                    <span className="flex items-center gap-2">
                      <Activity className="w-4 h-4 text-red-600" />
                      Earthquake Activity
                    </span>
                  </label>
                  <div className="flex gap-6">
                    <label className="flex items-center gap-2 cursor-pointer">
                      <input
                        type="radio"
                        value={0}
                        className="w-4 h-4 text-emerald-600 focus:ring-emerald-500 cursor-pointer"
                        {...register("earthquakeActivity", {
                          required: "Earthquake activity selection is required",
                          valueAsNumber: true
                        })}
                      />
                      <span className="text-gray-700">No (0)</span>
                    </label>
                    <label className="flex items-center gap-2 cursor-pointer">
                      <input
                        type="radio"
                        value={1}
                        className="w-4 h-4 text-emerald-600 focus:ring-emerald-500 cursor-pointer"
                        {...register("earthquakeActivity", {
                          required: "Earthquake activity selection is required",
                          valueAsNumber: true
                        })}
                      />
                      <span className="text-gray-700">Yes (1)</span>
                    </label>
                  </div>
                  {errors.earthquakeActivity && (
                    <p className="text-red-600 mt-1">{errors.earthquakeActivity.message}</p>
                  )}
                </div>
              </div>
            </div>

            {/* Soil Parameters Section */}
            <div className="mb-8">
              <div className="flex items-center gap-2 mb-5 pb-2 border-b-2 border-stone-300">
                <Droplets className="w-5 h-5 text-stone-600" />
                <h2 className="text-stone-900">Soil Parameters</h2>
              </div>
              
              <div className="space-y-5">
                {/* Soil Saturation */}
                <div>
                  <label htmlFor="soilSaturation" className="block text-gray-700 mb-2">
                    Soil Saturation (0 - 1)
                  </label>
                  <input
                    id="soilSaturation"
                    type="number"
                    step="0.01"
                    placeholder="e.g., 0.0 (dry) to 1.0 (saturated)"
                    className="w-full px-4 py-3 bg-gray-50 border border-gray-300 rounded-lg focus:ring-2 focus:ring-emerald-500 focus:border-emerald-500 outline-none transition-all"
                    {...register("soilSaturation", {
                      required: "Soil saturation is required",
                      min: { value: 0, message: "Must be between 0 and 1" },
                      max: { value: 1, message: "Must be between 0 and 1" },
                      valueAsNumber: true
                    })}
                  />
                  {errors.soilSaturation && (
                    <p className="text-red-600 mt-1">{errors.soilSaturation.message}</p>
                  )}
                </div>

                {/* Soil Type */}
                <div>
                  <label className="block text-gray-700 mb-2">
                    Soil Type
                  </label>
                  <div className="space-y-3">
                    <label className="flex items-center gap-3 p-3 bg-gray-50 rounded-lg cursor-pointer hover:bg-gray-100 transition-colors border border-gray-200">
                      <input
                        type="radio"
                        value="gravel"
                        className="w-4 h-4 text-emerald-600 focus:ring-emerald-500 cursor-pointer"
                        {...register("soilType", {
                          required: "Soil type is required"
                        })}
                      />
                      <span className="text-gray-700">Gravel</span>
                    </label>
                    <label className="flex items-center gap-3 p-3 bg-gray-50 rounded-lg cursor-pointer hover:bg-gray-100 transition-colors border border-gray-200">
                      <input
                        type="radio"
                        value="sand"
                        className="w-4 h-4 text-emerald-600 focus:ring-emerald-500 cursor-pointer"
                        {...register("soilType", {
                          required: "Soil type is required"
                        })}
                      />
                      <span className="text-gray-700">Sand</span>
                    </label>
                    <label className="flex items-center gap-3 p-3 bg-gray-50 rounded-lg cursor-pointer hover:bg-gray-100 transition-colors border border-gray-200">
                      <input
                        type="radio"
                        value="silt"
                        className="w-4 h-4 text-emerald-600 focus:ring-emerald-500 cursor-pointer"
                        {...register("soilType", {
                          required: "Soil type is required"
                        })}
                      />
                      <span className="text-gray-700">Silt</span>
                    </label>
                  </div>
                  {errors.soilType && (
                    <p className="text-red-600 mt-1">{errors.soilType.message}</p>
                  )}
                </div>
              </div>
            </div>

            {/* Submit Button */}
            <button
              type="submit"
              className="w-full bg-gradient-to-r from-emerald-600 to-teal-600 hover:from-emerald-700 hover:to-teal-700 text-white py-4 px-6 rounded-lg transition-all duration-200 shadow-lg hover:shadow-xl"
            >
              Predict Landslide Risk
            </button>
          </form>

          {/* Prediction Result */}
          {prediction && (
            <div className={`mx-8 mb-8 p-6 rounded-xl border-2 ${
              prediction.risk 
                ? 'bg-red-50 border-red-300' 
                : 'bg-green-50 border-green-300'
            }`}>
              <div className="flex items-center gap-3 mb-3">
                {prediction.risk ? (
                  <CircleAlert className="w-8 h-8 text-red-600" />
                ) : (
                  <CircleCheck className="w-8 h-8 text-green-600" />
                )}
                <h3 className={prediction.risk ? 'text-red-900' : 'text-green-900'}>
                  {prediction.risk ? 'Landslide Risk Detected' : 'No Landslide Risk'}
                </h3>
              </div>
              <p className={`${prediction.risk ? 'text-red-800' : 'text-green-800'} mb-2`}>
                {prediction.risk 
                  ? 'The current conditions indicate a significant risk of landslide occurrence. Immediate precautionary measures are recommended.'
                  : 'The current conditions suggest low landslide risk. Continue monitoring environmental parameters.'}
              </p>
              <div className="mt-4">
                <div className="flex justify-between items-center mb-2">
                  <span className={`${prediction.risk ? 'text-red-700' : 'text-green-700'}`}>
                    Risk Score:
                  </span>
                  <span className={`${prediction.risk ? 'text-red-900' : 'text-green-900'}`}>
                    {prediction.riskScore.toFixed(1)}%
                  </span>
                </div>
                <div className="w-full bg-gray-200 rounded-full h-3 overflow-hidden">
                  <div 
                    className={`h-full transition-all duration-500 ${
                      prediction.risk 
                        ? 'bg-gradient-to-r from-red-500 to-red-600' 
                        : 'bg-gradient-to-r from-green-500 to-green-600'
                    }`}
                    style={{ width: `${prediction.riskScore}%` }}
                  />
                </div>
              </div>
            </div>
          )}
        </div>

        {/* Footer Info */}
        <div className="text-center mt-6 text-muted-foreground">
          <p>Environmental Monitoring & Disaster Prevention System</p>
        </div>
      </div>
    </div>
  );
}
import { useState } from 'react';

export default function App() {
  // Change this to 'high' or 'low' to see different results
  // In your actual implementation, this would come from the previous page
  const [riskLevel, setRiskLevel] = useState<'high' | 'low' | null>('high');

  return (
    <div 
      className="size-full flex items-center justify-center relative overflow-hidden"
      style={{
        backgroundImage: `url('https://images.unsplash.com/photo-1668784317591-14bc5213bcbf?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w3Nzg4Nzd8MHwxfHNlYXJjaHwxfHxsYW5kc2xpZGUlMjBtb3VudGFpbiUyMHRlcnJhaW58ZW58MXx8fHwxNzY2NzU4NTY1fDA&ixlib=rb-4.1.0&q=80&w=1080&utm_source=figma&utm_medium=referral')`,
        backgroundSize: 'cover',
        backgroundPosition: 'center',
      }}
    >
      <div className="absolute inset-0 bg-black/50" />
      
      <div className="relative z-10 max-w-4xl mx-auto px-8 text-center">
        <div className="space-y-12 mb-16">
          {/* Yes Option - High Risk */}
          <div 
            className={`p-8 rounded-2xl border-4 shadow-2xl transform transition-all duration-500 ${
              riskLevel === 'high' 
                ? 'bg-red-600/90 border-red-400 scale-110' 
                : 'bg-gray-500/50 border-gray-400 scale-100'
            }`}
          >
            <div className="flex items-center justify-center gap-4">
              {riskLevel === 'high' && <span className="text-6xl">⚠️</span>}
              <div>
                <h2 className="text-5xl text-white mb-2">YES!</h2>
                <p className="text-3xl text-white/90">High Landslide Risk</p>
              </div>
              {riskLevel === 'high' && <span className="text-6xl">⚠️</span>}
            </div>
          </div>

          {/* No Option - Low Risk */}
          <div 
            className={`p-8 rounded-2xl border-4 shadow-2xl transform transition-all duration-500 ${
              riskLevel === 'low' 
                ? 'bg-green-600/90 border-green-400 scale-110' 
                : 'bg-gray-500/50 border-gray-400 scale-100'
            }`}
          >
            <div className="flex items-center justify-center gap-4">
              {riskLevel === 'low' && <span className="text-6xl">✓</span>}
              <div>
                <h2 className="text-5xl text-white mb-2">NO!</h2>
                <p className="text-3xl text-white/90">Low Landslide Risk</p>
              </div>
              {riskLevel === 'low' && <span className="text-6xl">✓</span>}
            </div>
          </div>
        </div>

        {/* Next Button */}
        <button 
          className="bg-blue-600 hover:bg-blue-700 text-white px-16 py-6 rounded-full text-3xl shadow-2xl border-4 border-blue-400 transform hover:scale-110 transition-all"
        >
          Next →
        </button>

        {/* Demo buttons - Remove these in production */}
        <div className="mt-8 flex gap-4 justify-center">
          <button
            onClick={() => setRiskLevel('high')}
            className="bg-white/20 hover:bg-white/30 text-white px-6 py-2 rounded-lg border border-white/50"
          >
            Demo: High Risk
          </button>
          <button
            onClick={() => setRiskLevel('low')}
            className="bg-white/20 hover:bg-white/30 text-white px-6 py-2 rounded-lg border border-white/50"
          >
            Demo: Low Risk
          </button>
        </div>
      </div>
    </div>
  );
}
import { Card, CardContent, CardHeader, CardTitle } from "./components/ui/card";
import { 
  TriangleAlert, 
  Shield,
  CheckCircle,
  AlertCircle,
  Info,
  Phone,
  MapPin,
  Clock,
  FileText,
  Home,
  Waves,
  Mountain,
  Radio,
  Users,
  Bell,
  Eye,
  ArrowRight
} from "lucide-react";
import { Alert, AlertDescription, AlertTitle } from "./components/ui/alert";

export default function App() {
  const beforePrecautions = [
    {
      icon: Eye,
      text: "Learn warning signs: cracks, tilting trees, water changes"
    },
    {
      icon: MapPin,
      text: "Know evacuation routes and safe areas"
    },
    {
      icon: FileText,
      text: "Prepare emergency kit with essentials"
    },
    {
      icon: Mountain,
      text: "Avoid building on steep slopes"
    },
    {
      icon: Waves,
      text: "Install proper drainage systems"
    },
    {
      icon: Users,
      text: "Create family emergency plan"
    }
  ];

  const duringPrecautions = [
    {
      icon: AlertCircle,
      text: "Evacuate immediately if ground moves"
    },
    {
      icon: ArrowRight,
      text: "Move away from landslide path quickly"
    },
    {
      icon: Mountain,
      text: "Run to higher ground perpendicular to slide"
    },
    {
      icon: Bell,
      text: "Alert neighbors and call 100"
    },
    {
      icon: Home,
      text: "If trapped indoors, move to upper floor"
    },
    {
      icon: Radio,
      text: "Listen to emergency broadcasts"
    }
  ];

  const afterPrecautions = [
    {
      icon: TriangleAlert,
      text: "Stay away - more slides may occur"
    },
    {
      icon: Eye,
      text: "Watch for flooding after landslide"
    },
    {
      icon: Phone,
      text: "Check for injuries, provide first aid"
    },
    {
      icon: AlertCircle,
      text: "Report damaged utilities immediately"
    },
    {
      icon: Home,
      text: "Don't enter damaged buildings"
    },
    {
      icon: Users,
      text: "Follow official instructions"
    }
  ];

  return (
    <div className="min-h-screen bg-gradient-to-br from-gray-50 to-gray-100">
      {/* Header */}
      <header className="bg-white border-b-4 border-red-600 shadow-md">
        <div className="container mx-auto px-6 py-8">
          <div className="flex items-center justify-between">
            <div className="flex items-center gap-4">
              <div className="w-16 h-16 bg-gradient-to-br from-red-600 to-orange-600 rounded-xl flex items-center justify-center shadow-lg">
                <Shield className="w-9 h-9 text-white" />
              </div>
              <div>
                <h1 className="text-4xl font-bold text-gray-900">
                  Landslide Safety Precautions
                </h1>
                <p className="text-gray-600 mt-1">
                  Essential guidelines to protect yourself and your community
                </p>
              </div>
            </div>
            <div className="text-right">
              <div className="inline-flex items-center gap-2 bg-red-100 text-red-700 px-4 py-2 rounded-lg border border-red-300">
                <Bell className="w-5 h-5" />
                <span className="font-semibold">Stay Alert</span>
              </div>
            </div>
          </div>
        </div>
      </header>

      <main className="container mx-auto px-6 py-10">
        {/* Emergency Banner */}
        <Alert className="mb-10 bg-gradient-to-r from-red-50 to-orange-50 border-l-4 border-red-600 shadow-md">
          <TriangleAlert className="h-6 w-6 text-red-600" />
          <AlertTitle className="text-lg font-bold text-gray-900">
            Emergency Alert Information
          </AlertTitle>
          <AlertDescription className="text-gray-700 mt-2">
            Landslides can occur suddenly with little warning. Familiarize yourself with these precautions 
            and share them with your family and neighbors. In case of emergency, call <strong>100</strong> immediately.
          </AlertDescription>
        </Alert>

        {/* Emergency Contacts Section */}
        <div className="grid grid-cols-1 md:grid-cols-3 gap-6 mb-10">
          <Card className="border-2 border-red-200 shadow-lg hover:shadow-xl transition-shadow">
            <CardContent className="pt-6">
              <div className="flex items-center gap-3">
                <div className="w-12 h-12 bg-red-600 rounded-lg flex items-center justify-center">
                  <Phone className="w-6 h-6 text-white" />
                </div>
                <div>
                  <p className="text-sm text-gray-600">Emergency Services</p>
                  <p className="text-2xl font-bold text-red-600">100</p>
                </div>
              </div>
            </CardContent>
          </Card>

          <Card className="border-2 border-blue-200 shadow-lg hover:shadow-xl transition-shadow">
            <CardContent className="pt-6">
              <div className="flex items-center gap-3">
                <div className="w-12 h-12 bg-blue-600 rounded-lg flex items-center justify-center">
                  <Radio className="w-6 h-6 text-white" />
                </div>
                <div>
                  <p className="text-sm text-gray-600">Ambulance</p>
                  <p className="text-xl font-bold text-blue-600">102</p>
                </div>
              </div>
            </CardContent>
          </Card>

          <Card className="border-2 border-green-200 shadow-lg hover:shadow-xl transition-shadow">
            <CardContent className="pt-6">
              <div className="flex items-center gap-3">
                <div className="w-12 h-12 bg-green-600 rounded-lg flex items-center justify-center">
                  <Info className="w-6 h-6 text-white" />
                </div>
                <div>
                  <p className="text-sm text-gray-600">Fire Service</p>
                  <p className="text-xl font-bold text-green-600">101</p>
                </div>
              </div>
            </CardContent>
          </Card>
        </div>

        {/* Main Precautions Sections */}
        <div className="space-y-8">
          {/* BEFORE Section */}
          <Card className="border-2 border-green-300 shadow-xl overflow-hidden">
            <CardHeader className="bg-gradient-to-r from-green-600 to-green-500 text-white py-6">
              <CardTitle className="text-2xl flex items-center gap-3">
                <div className="w-12 h-12 bg-white/20 rounded-lg flex items-center justify-center">
                  <CheckCircle className="w-7 h-7" />
                </div>
                <div>
                  <div className="text-3xl font-bold">Before a Landslide</div>
                  <div className="text-green-100 text-sm mt-1 font-normal">
                    Preparation and Prevention
                  </div>
                </div>
              </CardTitle>
            </CardHeader>
            <CardContent className="p-8 bg-green-50/50">
              <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                {beforePrecautions.map((precaution, index) => {
                  const Icon = precaution.icon;
                  return (
                    <div 
                      key={index} 
                      className="flex items-start gap-4 p-4 bg-white rounded-lg border border-green-200 hover:border-green-400 hover:shadow-md transition-all"
                    >
                      <div className="w-10 h-10 bg-green-100 rounded-lg flex items-center justify-center flex-shrink-0">
                        <Icon className="w-5 h-5 text-green-700" />
                      </div>
                      <p className="text-gray-700 leading-relaxed pt-1.5">{precaution.text}</p>
                    </div>
                  );
                })}
              </div>
            </CardContent>
          </Card>

          {/* DURING Section */}
          <Card className="border-2 border-yellow-300 shadow-xl overflow-hidden">
            <CardHeader className="bg-gradient-to-r from-yellow-500 to-orange-500 text-white py-6">
              <CardTitle className="text-2xl flex items-center gap-3">
                <div className="w-12 h-12 bg-white/20 rounded-lg flex items-center justify-center">
                  <TriangleAlert className="w-7 h-7" />
                </div>
                <div>
                  <div className="text-3xl font-bold">During a Landslide</div>
                  <div className="text-yellow-100 text-sm mt-1 font-normal">
                    Immediate Action Required
                  </div>
                </div>
              </CardTitle>
            </CardHeader>
            <CardContent className="p-8 bg-yellow-50/50">
              <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                {duringPrecautions.map((precaution, index) => {
                  const Icon = precaution.icon;
                  return (
                    <div 
                      key={index} 
                      className="flex items-start gap-4 p-4 bg-white rounded-lg border border-yellow-200 hover:border-yellow-400 hover:shadow-md transition-all"
                    >
                      <div className="w-10 h-10 bg-yellow-100 rounded-lg flex items-center justify-center flex-shrink-0">
                        <Icon className="w-5 h-5 text-yellow-700" />
                      </div>
                      <p className="text-gray-700 leading-relaxed pt-1.5">{precaution.text}</p>
                    </div>
                  );
                })}
              </div>
            </CardContent>
          </Card>

          {/* AFTER Section */}
          <Card className="border-2 border-red-300 shadow-xl overflow-hidden">
            <CardHeader className="bg-gradient-to-r from-red-600 to-red-500 text-white py-6">
              <CardTitle className="text-2xl flex items-center gap-3">
                <div className="w-12 h-12 bg-white/20 rounded-lg flex items-center justify-center">
                  <AlertCircle className="w-7 h-7" />
                </div>
                <div>
                  <div className="text-3xl font-bold">After a Landslide</div>
                  <div className="text-red-100 text-sm mt-1 font-normal">
                    Recovery and Safety Measures
                  </div>
                </div>
              </CardTitle>
            </CardHeader>
            <CardContent className="p-8 bg-red-50/50">
              <div className="grid grid-cols-1 md:grid-cols-2 gap-6">
                {afterPrecautions.map((precaution, index) => {
                  const Icon = precaution.icon;
                  return (
                    <div 
                      key={index} 
                      className="flex items-start gap-4 p-4 bg-white rounded-lg border border-red-200 hover:border-red-400 hover:shadow-md transition-all"
                    >
                      <div className="w-10 h-10 bg-red-100 rounded-lg flex items-center justify-center flex-shrink-0">
                        <Icon className="w-5 h-5 text-red-700" />
                      </div>
                      <p className="text-gray-700 leading-relaxed pt-1.5">{precaution.text}</p>
                    </div>
                  );
                })}
              </div>
            </CardContent>
          </Card>
        </div>

        {/* Additional Resources */}
        <div className="mt-10 grid grid-cols-1 md:grid-cols-3 gap-6">
          <Card className="border-2 border-gray-200 shadow-lg hover:shadow-xl transition-shadow bg-white">
            <CardContent className="pt-6 pb-6 text-center">
              <div className="w-14 h-14 bg-blue-100 rounded-full flex items-center justify-center mx-auto mb-4">
                <Home className="w-7 h-7 text-blue-600" />
              </div>
              <h3 className="font-bold text-gray-900 mb-2">Evacuation Centers</h3>
              <p className="text-sm text-gray-600">
                Find safe shelters and evacuation points near you
              </p>
            </CardContent>
          </Card>

          <Card className="border-2 border-gray-200 shadow-lg hover:shadow-xl transition-shadow bg-white">
            <CardContent className="pt-6 pb-6 text-center">
              <div className="w-14 h-14 bg-purple-100 rounded-full flex items-center justify-center mx-auto mb-4">
                <FileText className="w-7 h-7 text-purple-600" />
              </div>
              <h3 className="font-bold text-gray-900 mb-2">Emergency Kit Guide</h3>
              <p className="text-sm text-gray-600">
                Learn what supplies to include in your kit
              </p>
            </CardContent>
          </Card>

          <Card className="border-2 border-gray-200 shadow-lg hover:shadow-xl transition-shadow bg-white">
            <CardContent className="pt-6 pb-6 text-center">
              <div className="w-14 h-14 bg-orange-100 rounded-full flex items-center justify-center mx-auto mb-4">
                <Users className="w-7 h-7 text-orange-600" />
              </div>
              <h3 className="font-bold text-gray-900 mb-2">Community Support</h3>
              <p className="text-sm text-gray-600">
                Connect with local disaster response teams
              </p>
            </CardContent>
          </Card>
        </div>

        {/* Important Notice */}
        <Card className="mt-10 border-2 border-blue-300 bg-gradient-to-r from-blue-50 to-blue-100">
          <CardContent className="pt-6 pb-6">
            <div className="flex items-start gap-4">
              <Info className="w-8 h-8 text-blue-600 flex-shrink-0 mt-1" />
              <div>
                <h3 className="font-bold text-blue-900 text-lg mb-2">Important Notice</h3>
                <p className="text-blue-800 leading-relaxed">
                  This information is provided as a general guideline for landslide safety. Always follow the 
                  instructions of local authorities and emergency services. Stay informed through official channels 
                  and emergency alert systems. If you live in a landslide-prone area, consider consulting with 
                  geological experts about specific risks to your property.
                </p>
              </div>
            </div>
          </CardContent>
        </Card>
      </main>

      {/* Footer */}
      <footer className="bg-gray-900 text-white mt-16">
        <div className="container mx-auto px-6 py-8">
          <div className="flex flex-col md:flex-row justify-between items-center gap-4">
            <div className="flex items-center gap-3">
              <Shield className="w-6 h-6 text-red-500" />
              <div className="text-sm text-gray-400">
                <p className="font-semibold text-white">National Disaster Management Authority</p>
                <p>Protecting communities through preparedness and awareness</p>
              </div>
            </div>
            <div className="text-sm text-gray-400">
              <p>© 2024 NDMA. All rights reserved.</p>
              <p className="flex items-center gap-2 mt-1">
                <Clock className="w-4 h-4" />
                Last updated: December 22, 2024
              </p>
            </div>
          </div>
        </div>
      </footer>
    </div>
  );
}
